from django.contrib.auth.backends import BaseBackend
from django.contrib.auth.models import User
from ldap3 import Server, Connection, AUTO_BIND, ALL, NTLM

class ActiveDirectoryBackend(BaseBackend):
    """
    Custom authentication backend for Active Directory.
    """

    def authenticate(self, request, username=None, password=None):
        AD_SERVER = "ldap://your-ad-server.com"  # Change to your AD server
        AD_DOMAIN = "yourdomain.com"  # Change to your domain
        AD_SEARCH_BASE = "OU=Users,DC=yourdomain,DC=com"  # Change to your AD structure
        AD_GROUP = "CN=YourADGroup,OU=Groups,DC=yourdomain,DC=com"  # Change to your group
        
        # Construct user DN
        user_dn = f"{AD_DOMAIN}\\{username}"
        
        try:
            # Connect to AD and authenticate
            server = Server(AD_SERVER, get_info=ALL)
            conn = Connection(server, user=user_dn, password=password, authentication=NTLM, auto_bind=True)

            # Check if the user belongs to the specified group
            conn.search(
                search_base=AD_SEARCH_BASE,
                search_filter=f"(sAMAccountName={username})",
                attributes=["memberOf"],
            )

            if not conn.entries:
                return None

            # Extract group memberships
            user_groups = conn.entries[0]["memberOf"]

            # Check if user is in the required AD group
            if any(AD_GROUP in str(group) for group in user_groups):
                # Check if user exists in Django, if not, create one
                user, created = User.objects.get_or_create(username=username)
                return user
            else:
                return None
        except Exception as e:
            print(f"AD Authentication failed: {e}")
            return None

    def get_user(self, user_id):
        try:
            return User.objects.get(pk=user_id)
        except User.DoesNotExist:
            return None