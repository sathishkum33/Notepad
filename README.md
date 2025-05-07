from django.contrib.auth.decorators import user_passes_test
from django.shortcuts import render, redirect
from .models import Application, Role, UserApplicationRole
from django.contrib.auth.models import User

@user_passes_test(lambda u: u.is_superuser)  # You can change this to a permission check
def manage_roles_view(request):
    users = User.objects.all()
    apps = Application.objects.all()
    roles = Role.objects.all()
    message = ""

    if request.method == "POST":
        user_id = request.POST.get("user")
        app_id = request.POST.get("application")
        selected_roles = request.POST.getlist("roles")

        user = User.objects.get(id=user_id)
        app = Application.objects.get(id=app_id)

        # Remove old roles for this user-app pair
        UserApplicationRole.objects.filter(user=user, application=app).delete()

        # Assign new roles
        for role_id in selected_roles:
            role = Role.objects.get(id=role_id)
            UserApplicationRole.objects.create(user=user, application=app, role=role)

        message = "Roles updated successfully."

    return render(request, "manage_roles.html", {
        "users": users,
        "apps": apps,
        "roles": roles,
        "message": message
    })