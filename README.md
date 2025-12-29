import socket
import subprocess
import platform
from concurrent.futures import ThreadPoolExecutor, as_completed
import psycopg2
from psycopg2.extras import execute_batch

# -----------------------------------
# CONFIG
# -----------------------------------

MAX_WORKERS = 50
PING_TIMEOUT = 2
PORT_TIMEOUT = 3
SSH_PORT = 22


def db_config():
    return {
        "host": "DB_HOST",
        "database": "DB_NAME",
        "user": "DB_USER",
        "password": "DB_PASSWORD",
        "port": 5432
    }


# ===================================
# PUBLIC FUNCTION (CALL THIS ONLY)
# ===================================

def get_server_details():
    """
    One-call function:
    - Fetch servers
    - Check status concurrently
    - Update DB
    """
    servers = _fetch_servers()
    if not servers:
        return []

    results = _check_server_status(servers)
    _update_db_status(results)

    return results


# ===================================
# INTERNAL FUNCTIONS
# ===================================

def _fetch_servers():
    conn = None
    try:
        conn = psycopg2.connect(**db_config())
        cur = conn.cursor()
        cur.execute('SELECT hostname FROM reports_app_servertbl')
        return [row[0] for row in cur.fetchall()]
    finally:
        if conn:
            conn.close()


def _check_server_status(hostnames):
    results = []

    def _check(host):
        if not _ping_host(host):
            return host, "Down"
        if not _is_port_open(host):
            return host, "Down"
        return host, "UP"

    with ThreadPoolExecutor(max_workers=MAX_WORKERS) as executor:
        futures = [executor.submit(_check, host) for host in hostnames]

        for future in as_completed(futures):
            try:
                results.append(future.result())
            except Exception:
                pass

    return results


def _update_db_status(results):
    if not results:
        return

    conn = None
    try:
        conn = psycopg2.connect(**db_config())
        cur = conn.cursor()

        query = """
            UPDATE reports_app_servertbl
            SET "Status" = %s
            WHERE hostname = %s
        """

        execute_batch(cur, query, [(status, host) for host, status in results])
        conn.commit()

    finally:
        if conn:
            conn.close()


# ===================================
# NETWORK HELPERS
# ===================================

def _ping_host(hostname):
    param = "-n" if platform.system().lower() == "windows" else "-c"
    cmd = ["ping", param, "1", "-W", str(PING_TIMEOUT), hostname]

    try:
        return subprocess.run(
            cmd,
            stdout=subprocess.DEVNULL,
            stderr=subprocess.DEVNULL
        ).returncode == 0
    except Exception:
        return False


def _is_port_open(hostname, port=SSH_PORT):
    try:
        with socket.create_connection((hostname, port), timeout=PORT_TIMEOUT):
            return True
    except Exception:
        return False