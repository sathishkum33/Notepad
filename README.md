from ldap3 import Server, Connection, SIMPLE, AUTO_BIND_NO_TLS

server = Server("ldap://your-ad-server.com", get_info="ALL")

conn = Connection(
    server,
    user="CN=YourAdmin,CN=Users,DC=yourdomain,DC=com",  # Full DN of an admin user
    password="yourpassword",
    authentication=SIMPLE,
    auto_bind=AUTO_BIND_NO_TLS
)