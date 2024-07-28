import re

# Define regex patterns
hostname_pattern = re.compile(r'(?i)(hostname|host):?\s*([\w.-]+)(?::(\d{1,5}))?')
port_pattern = re.compile(r'(?i)(port):?\s*(\d{1,5})')
username_pattern = re.compile(r'(?i)(user|username):?\s*([\w.-]+)')
dbname_pattern = re.compile(r'(?i)(database|dbname):?\s*([\w.-]+)(?::(\d{1,5}))?')

# Function to extract details from description
def extract_details(description):
    hostname = None
    port = None
    username = None
    dbname = None
    
    hostname_match = hostname_pattern.search(description)
    port_match = port_pattern.search(description)
    username_match = username_pattern.search(description)
    dbname_match = dbname_pattern.search(description)
    
    if hostname_match:
        hostname = hostname_match.group(2)
        if not port and hostname_match.group(3):
            port = hostname_match.group(3)
    if port_match:
        port = port_match.group(2)
    if username_match:
        username = username_match.group(2)
    if dbname_match:
        dbname = dbname_match.group(2)
        if not port and dbname_match.group(3):
            port = dbname_match.group(3)
        
    return hostname, port, username, dbname

# Example usage
description1 = "Please execute the query on the following DB: hostname: db.example.com, port: 5432, user: admin, database: test_db"
hostname1, port1, username1, dbname1 = extract_details(description1)
print(f"Hostname: {hostname1}, Port: {port1}, Username: {username1}, Database: {dbname1}")

# Another example usage with combined db name and port
description2 = "Execute query on database: example.db.com:15005"
hostname2, port2, username2, dbname2 = extract_details(description2)
print(f"Hostname: {hostname2}, Port: {port2}, Username: {username2}, Database: {dbname2}")
