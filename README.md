You are an expert Python and PostgreSQL engineer. Generate a production-grade
server health-check script with the following exact requirements:

─────────────────────────────────────────────
CONTEXT
─────────────────────────────────────────────
- I have an existing PostgreSQL database table named `servers` (schema already
  exists — do NOT generate or assume any CREATE TABLE statements).
- This script will be orchestrated as an Apache Airflow DAG (Airflow version 3.2.1).
- The goal is to check each server's reachability and update its status in bulk
  with minimal DB round-trips.

─────────────────────────────────────────────
SERVER STATUS LOGIC (strict sequence)
─────────────────────────────────────────────
For each server, perform checks in this exact order:

  STEP 1 — ICMP Ping:
    - Send an ICMP ping to the host.
    - If ping succeeds → status = "UP". Stop all further checks.

  STEP 2 — SSH TCP Socket Check (only if ICMP fails):
    Attempt a raw TCP socket connection to port 22 and interpret the
    result using these EXACT distinctions:

    CASE A — Connection Refused (TCP RST received):
      The OS on the remote machine actively sent a TCP RST packet,
      rejecting the connection. This proves the server is reachable
      and the network path is alive, but either no service is listening
      on port 22, or a firewall is explicitly configured to reject it.
      → status = "FW_ISSUE"

    CASE B — Connection Timeout (no reply within 5 seconds):
      The TCP SYN packet was sent but no response was received within
      the timeout window. This indicates a firewall is silently dropping
      the packet (SYN drop / blackhole route), or the network route to
      the host is dead.
      → status = "FW_ISSUE"

    CASE C — Any other socket error (host unreachable, DNS failure, etc.):
      The host cannot be contacted at the network level at all.
      → status = "DOWN"

─────────────────────────────────────────────
STATUS VALUES
─────────────────────────────────────────────
Use exactly these string values when updating the DB:
  - "UP"        → ICMP ping succeeded
  - "FW_ISSUE"  → ICMP failed + TCP RST received OR TCP connection timed out
  - "DOWN"      → ICMP failed + all TCP socket attempts failed with
                  a network-level error (unreachable, DNS failure, etc.)

─────────────────────────────────────────────
EXCEPTION HANDLING FOR TCP (critical)
─────────────────────────────────────────────
Map Python socket/asyncio exceptions to statuses precisely:

  asyncio.TimeoutError
    → Packet silently dropped or network route dead → "FW_ISSUE"

  ConnectionRefusedError (OSError with errno.ECONNREFUSED)
    → TCP RST received, server is alive → "FW_ISSUE"

  OSError with errno in (errno.EHOSTUNREACH, errno.ENETUNREACH,
  errno.ENONET, errno.ETIMEDOUT)
    → Host or network unreachable at IP level → "DOWN"

  socket.gaierror (DNS resolution failure)
    → Cannot resolve hostname → "DOWN"

  Any other unexpected exception
    → Log the error with host + exception detail → "DOWN"

─────────────────────────────────────────────
PERFORMANCE REQUIREMENTS
─────────────────────────────────────────────
- Run all host checks CONCURRENTLY using asyncio.
- Use a Semaphore to cap concurrency (default: 200 simultaneous checks).
- Per-host ICMP timeout: 5 seconds.
- Per-host TCP socket timeout: 5 seconds (drives the TimeoutError above).
- Fetch all servers from DB in a SINGLE SELECT query.
- After checks complete, perform a SINGLE BULK UPDATE using PostgreSQL's
  unnest() array approach — do NOT loop individual UPDATE statements.
- Only write rows where status has CHANGED since last fetch
  (diff-check in Python before issuing the UPDATE).
- Use pg_try_advisory_lock to prevent overlapping DAG runs.

─────────────────────────────────────────────
AIRFLOW DAG REQUIREMENTS (Airflow 3.2.1)
─────────────────────────────────────────────
- Use the TaskFlow API (@dag, @task decorators).
- DAG schedule: every 5 minutes (schedule="*/5 * * * *").
- max_active_runs=1 to prevent concurrent DAG runs.
- Structure tasks as a clean pipeline:
    [fetch_servers] → [check_all_servers] → [bulk_update_db] → [log_summary]
- Pass data between tasks using XCom (return values from @task).
- Set retries=1, retry_delay=30s on the bulk_update_db task only.
- Read the DB connection string from an Airflow Variable named
  "SERVER_MONITOR_DB_DSN" or fall back to the environment variable
  "SERVER_MONITOR_DB_DSN".
- The log_summary @task must emit:
    total checked | UP | DOWN | FW_ISSUE | changed (written to DB) | elapsed time

─────────────────────────────────────────────
CODE QUALITY REQUIREMENTS
─────────────────────────────────────────────
- Use asyncpg for all PostgreSQL interactions (async, connection pool).
- Use asyncio for all concurrency — no third-party task queues.
- Use icmplib (async-compatible) for ICMP ping.
- Use asyncio.open_connection() for raw TCP socket checks.
- Structured logging at every stage: fetch, per-host result, bulk update,
  and final summary.
- One failed host must NEVER crash the entire batch — wrap per-host logic
  in try/except and always return a result.
- Add clear inline comments explaining the status decision logic,
  especially the TCP exception-to-status mapping.

─────────────────────────────────────────────
DELIVERABLES
─────────────────────────────────────────────
1. Python — Single Airflow 3.2.1 DAG file using TaskFlow API.
2. Requirements — pip install command for all dependencies.
3. Config — Airflow Variable and environment variable names needed.

DO NOT generate any SQL DDL (CREATE TABLE, ALTER TABLE, etc.).
The database schema already exists.