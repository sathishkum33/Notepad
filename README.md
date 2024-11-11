POST /_security/role_mapping/admin_role
{
  "roles": [ "superuser", "kibana_admin" ],
  "enabled": true,
  "rules": {
    "any": [
      { "field": { "username": "svc-account@your-domain.com" } },
      { "field": { "groups": "CN=adgroup,OU=Applications,OU=Groups,DC=your-domain,DC=com" } }
    ]
  }
}