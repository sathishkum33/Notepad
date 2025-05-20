def user_has_role(user, app_name, role_name):
    return AppRole.objects.filter(
        user=user,
        application__name=app_name,
        role__name=role_name
    ).exists()