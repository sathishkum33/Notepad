import socket

def check_ports(ip):
    ports = {
        22: "SSH (Linux)",
        3389: "RDP (Windows)",
        445: "SMB (Windows)",
        135: "RPC (Windows)",
        111: "RPCBind (Linux)"
    }

    for port, label in ports.items():
        try:
            sock = socket.create_connection((ip, port), timeout=2)
            print(f"{label} - Port {port} is open -> Possible {label.split()[1]}")
            sock.close()
        except:
            pass

# Example usage
check_ports("192.168.1.100")