# Enable LDAP Authentication
AUTHENTICATION_BACKENDS = [
    "django_python3_ldap.auth.LDAPBackend",
    "django.contrib.auth.backends.ModelBackend",
]

# LDAP Server Configuration
LDAP_AUTH_URL = "ldap://your-ad-server.com"  # or "ldaps://your-ad-server.com" for SSL

# Bind credentials (optional, can be anonymous)
LDAP_AUTH_CONNECTION_USERNAME = "CN=youradmin,CN=Users,DC=yourdomain,DC=com"
LDAP_AUTH_CONNECTION_PASSWORD = "yourpassword"

# Search Base
LDAP_AUTH_SEARCH_BASE = "CN=Users,DC=yourdomain,DC=com"

# Define the user lookup
LDAP_AUTH_USER_LOOKUP_FIELDS = ("username",)

# Attribute mapping (optional)
LDAP_AUTH_USER_FIELDS = {
    "username": "sAMAccountName",
    "first_name": "givenName",
    "last_name": "sn",
    "email": "mail",
}

# Cache authentication results (optional)
LDAP_AUTH_CACHE_TIMEOUT = 3600  # 1 hour