import winrm

def fetch_windows_system_info(host, username, password):
    try:
        session = winrm.Session(f'http://{host}:5985/wsman', auth=(username, password), transport='ntlm')

        # PowerShell command to get all required info in one go
        ps_script = """
        $cpu = Get-CimInstance Win32_Processor | Select-Object -First 1 Name,NumberOfCores
        $mem = Get-CimInstance Win32_ComputerSystem | Select-Object TotalPhysicalMemory
        $disk = Get-CimInstance Win32_LogicalDisk -Filter "DriveType=3" | Measure-Object -Property Size -Sum
        $os = Get-CimInstance Win32_OperatingSystem | Select-Object Caption, Version, OSArchitecture

        $result = [PSCustomObject]@{
            CPU_Name = $cpu.Name
            CPU_Cores = $cpu.NumberOfCores
            TotalMemoryGB = [math]::Round($mem.TotalPhysicalMemory / 1GB, 2)
            TotalDiskGB = [math]::Round($disk.Sum / 1GB, 2)
            OS = $os.Caption
            OS_Version = $os.Version
            Architecture = $os.OSArchitecture
        }
        $result | ConvertTo-Json -Depth 2
        """

        result = session.run_ps(ps_script)
        if result.status_code == 0:
            import json
            data = json.loads(result.std_out.decode())
            return data
        else:
            print("Error:", result.std_err.decode())
            return None
    except Exception as e:
        print("WinRM connection error:", e)
        return None

# Example usage
host = "192.168.1.100"
username = "your_username"
password = "your_password"

info = fetch_windows_system_info(host, username, password)
if info:
    print("System Info:")
    for k, v in info.items():
        print(f"{k}: {v}")