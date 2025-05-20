from django.http import HttpResponseForbidden
from functools import wraps

def role_required(app_name, role_name):
    def decorator(view_func):
        @wraps(view_func)
        def _wrapped_view(request, *args, **kwargs):
            if not user_has_role(request.user, app_name, role_name):
                return HttpResponseForbidden("Permission denied.")
            return view_func(request, *args, **kwargs)
        return _wrapped_view
    return decorator