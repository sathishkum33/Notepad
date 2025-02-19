AUTHENTICATION_BACKENDS = [
    "yourapp.authentication_backends.ActiveDirectoryBackend",  # Use your app name
    "django.contrib.auth.backends.ModelBackend",
]