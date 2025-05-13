import paramiko
import json

class ServerInspector:
    def __init__(self, ip, username, password, port=22):
        self.ip = ip
        self.username = username
        self.password = password
        self.port = port
        self.ssh = None
        self.os_type = "unknown"

    def connect(self):
        self.ssh = paramiko.SSHClient()
        self.ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
        try:
            self.ssh.connect(
                hostname=self.ip,
                username=self.username,
                password=self.password,
                port=self.port,
                timeout=10
            )
            return True
        except Exception as e:
            self.ssh = None
            self.error = str(e)
            return False

    def disconnect(self):
        if self.ssh:
            self.ssh.close()

    def get_os_type(self):
        output = self._exec("uname || ver")
        if "linux" in output.lower():
            self.os_type = "linux"
        elif "windows" in output.lower():
            self.os_type = "windows"
        elif "darwin" in output.lower():
            self.os_type = "macos"
        else:
            self.os_type = "unknown"
        return self.os_type

    def _exec(self, command):
        try:
            stdin, stdout, stderr = self.ssh.exec_command(command)
            return stdout.read().decode().strip()
        except Exception:
            return ""

    def _safe_exec(self, command, fallback="0", parse_func=lambda x: x.strip()):
        result = self._exec(command)
        try:
            return parse_func(result) if result else fallback
        except Exception:
            return fallback

    def collect_resources(self):
        if not self.ssh:
            return {"error": "Not connected"}
        self.get_os_type()

        if self.os_type in ["linux", "macos"]:
            return {
                "cpu_count": self._safe_exec("nproc"),
                "memory_total_mb": self._safe_exec(
                    "grep MemTotal /proc/meminfo",
                    parse_func=lambda x: str(int(x.split()[1]) // 1024)
                ),
                "disk_total_gb": self._safe_exec(
                    "df --total -BG | grep total",
                    parse_func=lambda x: x.split()[1].replace("G", "")
                )
            }
        elif self.os_type == "windows":
            return {
                "cpu_count": self._safe_exec(
                    "wmic cpu get NumberOfLogicalProcessors /value",
                    parse_func=lambda x: x.split("=")[-1]
                ),
                "memory_total_mb": self._safe_exec(
                    "wmic ComputerSystem get TotalPhysicalMemory",
                    parse_func=lambda x: str(int(int(x.splitlines()[-1]) / (1024 * 1024)))
                ),
                "disk_total_gb": self._safe_exec(
                    'powershell -command "(Get-PSDrive -PSProvider \'FileSystem\' | Measure-Object -Property Used -Sum).Sum / 1GB"',
                    parse_func=lambda x: x.strip().splitlines()[-1]
                )
            }
        else:
            return {"error": "Unsupported OS"}

    def inspect(self):
        if not self.connect():
            return {"error": f"SSH connection failed: {self.error}"}
        try:
            resources = self.collect_resources()
            return {"os": self.os_type, "resources": resources}
        finally:
            self.disconnect()