from django.contrib import admin
from .models import Application, Role, UserApplicationRole

@admin.register(Application)
class ApplicationAdmin(admin.ModelAdmin):
    list_display = ('name',)

@admin.register(Role)
class RoleAdmin(admin.ModelAdmin):
    list_display = ('name', 'priority')

@admin.register(UserApplicationRole)
class UserApplicationRoleAdmin(admin.ModelAdmin):
    list_display = ('user', 'application', 'role')
    list_filter = ('application', 'role', 'user')
    search_fields = ('user__username', 'application__name', 'role__name')