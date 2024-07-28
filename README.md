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
    if port_match:
        port = port_match.group(2)
    if username_match:
        username = username_match.group(2)
    if dbname_match:
        dbname = dbname_match.group(2)
        
    return hostname, port, username, dbname

# Example usage
description = "Please execute the query on the following DB: hostname: db.example.com, port: 5432, user: admin, database: test_db"
hostname, port, username, dbname = extract_details(description)
print(f"Hostname: {hostname}, Port: {port}, Username: {username}, Database: {dbname}")
