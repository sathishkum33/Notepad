import os
import django

# Set up Django environment
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "your_project.settings")
django.setup()

# Import authentication backend
from yourapp.authentication_backends import ActiveDirectoryBackend

# Test credentials (Replace with test credentials)
test_username = "your_ad_username"
test_password = "your_ad_password"

# Initialize backend and test authentication
backend = ActiveDirectoryBackend()
user = backend.authenticate(request=None, username=test_username, password=test_password)

if user:
    print(f"Authentication successful: {user.username}")
else:
    print("Authentication failed!")