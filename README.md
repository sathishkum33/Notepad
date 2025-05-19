import nmap
import platform
import subprocess
import socket
import re

def get_ttl(ip):
    try:
        # Cross-platform ping
        if platform.system().lower() == "windows":
            output = subprocess.check_output(["ping", "-n", "1", ip], timeout=3).decode()
        else:
            output = subprocess.check_output(["ping", "-c", "1", ip], timeout=3).decode()
        match = re.search(r"ttl[=|:](\d+)", output, re.IGNORECASE)
        if match:
            return int(match.group(1))
    except Exception:
        return None

def analyze_ttl(ttl):
    if ttl is None:
        return None
    elif ttl <= 64:
        return "Linux"
    elif ttl <= 128:
        return "Windows"
    elif ttl <= 255:
        return "Unix"
    return None

def scan_ports(ip):
    scanner = nmap.PortScanner()
    try:
        scanner.scan(ip, arguments='-Pn -p 22,135,139,445,3389,5985,111,80,443')
        ports = scanner[ip]['tcp']
        return ports
    except Exception as e:
        return {}

def identify_os(ip):
    os_score = {"Windows": 0, "Linux": 0}
    
    # Step 1: TTL hint
    ttl = get_ttl(ip)
    ttl_guess = analyze_ttl(ttl)
    if ttl_guess == "Windows":
        os_score["Windows"] += 1
    elif ttl_guess == "Linux":
        os_score["Linux"] += 1

    # Step 2: Port checks
    ports = scan_ports(ip)
    for port in ports:
        if port == 22:
            os_score["Linux"] += 2  # SSH default
        elif port == 3389 or port == 5985 or port == 445 or port == 135:
            os_score["Windows"] += 2
        elif port == 111:
            os_score["Linux"] += 1

    # Step 3: Decision
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