from django.contrib import admin
from django.contrib.auth.models import User
from django.contrib.auth.admin import UserAdmin as BaseUserAdmin
from .models import Application, Role, UserApplicationRole

class UserApplicationRoleInline(admin.TabularInline):
    model = UserApplicationRole
    extra = 1  # Show 1 empty row by default
    autocomplete_fields = ['application', 'role']

# Extend the existing UserAdmin
class UserAdmin(BaseUserAdmin):
    inlines = [UserApplicationRoleInline]

# Unregister and re-register the User model
admin.site.unregister(User)
admin.site.register(User, UserAdmin)