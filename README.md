import winrm

def verify_winrm_credentials(ip, username, password, use_https=False):
    try:
        protocol = 'https' if use_https else 'http'
        port = '5986' if use_https else '5985'
        url = f'{protocol}://{ip}:{port}/wsman'

        # Use NTLM if you're authenticating with a local or domain account
        session = winrm.Session(url, auth=(username, password), transport='ntlm')

        result = session.run_cmd('hostname')  # simple, safe test command

        if result.status_code == 0:
            print(f"SUCCESS: Authenticated as {username}, Hostname: {result.std_out.decode().strip()}")
            return True
        else:
            print(f"ERROR: Command failed, code {result.status_code}")
            return False

    except Exception as e:
        print(f"FAILED: {e}")
        return False

# Example usage
ip = "192.168.1.100"
username = "Administrator"           # or "DOMAIN\\username"
password = "your_password"

verify_winrm_credentials(ip, username, password)