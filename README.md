from django.db import models
from django.contrib.auth.models import User

class Application(models.Model):
    name = models.CharField(max_length=100, unique=True)

    def __str__(self):
        return self.name

class Role(models.Model):
    name = models.CharField(max_length=50, unique=True)
    priority = models.IntegerField(default=0)  # For role hierarchy, optional

    def __str__(self):
        return self.name

class UserApplicationRole(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    application = models.ForeignKey(Application, on_delete=models.CASCADE)
    role = models.ForeignKey(Role, on_delete=models.CASCADE)

    class Meta:
        unique_together = ('user', 'application', 'role')
        verbose_name = 'User Role Assignment'
        verbose_name_plural = 'User Role Assignments'

    def __str__(self):
        return f"{self.user.username} - {self.application.name} - {self.role.name}"