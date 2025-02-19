from django.contrib.auth.backends import BaseBackend
from django.contrib.auth.models import User
from ldap3 import Server, Connection, SIMPLE, AUTO_BIND_NO_TLS

class ActiveDirectoryBackend(BaseBackend):
    """
    Custom authentication backend for Active Directory.
    """

    def authenticate(self, request, username=None, password=None):
        # AD Server and Domain Configuration
        AD_SERVER = "ldap://your-ad-server.com"
        AD_SEARCH_BASE = "CN=Users,DC=yourdomain,DC=com"  # Change based on your AD structure
        AD_GROUP = "CN=YourADGroup,OU=Groups,DC=yourdomain,DC=com"  # AD Group to check
        
        # Construct User DN (try different formats if needed)
        user_dn = f"CN={username},{AD_SEARCH_BASE}"

        try:
            # Connect to AD and authenticate
            server = Server(AD_SERVER, get_info="ALL")
            conn = Connection(server, user=user_dn, password=password, authentication=SIMPLE, auto_bind=AUTO_BIND_NO_TLS)
            
            # Search for user to get group membership
            conn.search(
                search_base=AD_SEARCH_BASE,
                search_filter=f"(sAMAccountName={username})",
                attributes=["memberOf"],
            )

            if not conn.entries:
                return None  # User not found in AD

            # Extract group memberships
            user_groups = conn.entries[0]["memberOf"]

            # Check if user is in the required AD group
            if any(AD_GROUP in str(group) for group in user_groups):
                # Check if user exists in Django, if not, create one
                user, created = User.objects.get_or_create(username=username)
                return user
            else:
                return None  # User not in the required group

        except Exception as e:
            print(f"AD Authentication failed: {e}")
            return None

    def get_user(self, user_id):
        try:
            return User.objects.get(pk=user_id)
        except User.DoesNotExist:
            return None