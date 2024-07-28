hostname_pattern = re.compile(r'(?i)(hostname|host):?\s*([\w.-]+)(?::(\d{1,5}))?')
port_pattern = re.compile(r'(?i)(port):?\s*(\d{1,5})')
username_pattern = re.compile(r'(?i)(user|username):?\s*([\w.-]+)')
dbname_pattern = re.compile(r'(?i)(database|dbname):?\s*([\w.-]+)(?::(\d{1,5}))?')
