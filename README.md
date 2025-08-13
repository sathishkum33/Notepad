import socket

def ping_tcp(host, port=22, timeout=2):
    try:
        socket.setdefaulttimeout(timeout)
        with socket.create_connection((host, port)):
            return True
    except OSError:
        return False

# Example: test SSH port
print(ping_tcp("github.com", 22))   # True if SSH reachable
print(ping_tcp("example.com", 22))  # Might be False if port closed