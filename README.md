import socket
import subprocess
import platform
import re

def ping_ttl(ip):
    try:
        if platform.system().lower() == "windows":
            output = subprocess.check_output(["ping", "-n", "1", ip], timeout=3).decode()
        else:
            output = subprocess.check_output(["ping", "-c", "1", ip], timeout=3).decode()
        match = re.search(r"ttl[=|:](\d+)", output, re.IGNORECASE)
        if match:
            return int(match.group(1))
    except Exception:
        return None

def check_port(ip, port, timeout=2):
    try:
        with socket.create_connection((ip, port), timeout=timeout):
            return True
    except:
        return False

def identify_os(ip):
    os_score = {"Windows": 0, "Linux": 0}
    
    # TTL hint
    ttl = ping_ttl(ip)
    if ttl:
        if ttl <= 64:
            os_score["Linux"] += 1
        elif ttl <= 128:
            os_score["Windows"] += 1
    
    # Port checks
    ports = {
        22: "Linux",        # SSH
        3389: "Windows",    # RDP
        445: "Windows",     # SMB
        135: "Windows",     # RPC
        5985: "Windows",    # WinRM HTTP
        111: "Linux"        # RPCBind
    }

    for port, os_name in ports.items():
        if check_port(ip, port):
            os_score[os_name] += 2

    # Final decision
    if os_score["Windows"] > os_score["Linux"]:
        return "Windows"
    elif os_score["Linux"] > os_score["Windows"]:
        return "Linux"
    else:
        return "Unknown"

# Example usage
ip_address = "192.168.1.100"
result = identify_os(ip_address)
print(f"{ip_address} appears to be: {result}")