import requests
from requests.auth import HTTPBasicAuth
import json

# Define variables
organization = "your_organization_name"
project = "your_project_name"
work_item_id = 12345  # Replace with your work item ID
pat = "your_personal_access_token"  # Replace with your PAT
new_tag = "new-tag"  # Replace with the tag you want to add

# Create the PATCH request payload
payload = [
    {
        "op": "add",
        "path": "/fields/System.Tags",
        "value": new_tag
    }
]

# Construct the REST API URL
url = f"https://dev.azure.com/{organization}/{project}/_apis/wit/workitems/{work_item_id}?api-version=6.0"

# Make the PATCH request
response = requests.patch(
    url,
    auth=HTTPBasicAuth('', pat),
    headers={'Content-Type': 'application/json-patch+json'},
    data=json.dumps(payload)
)

# Output the response
if response.status_code == 200:
    print("Tag updated successfully.")
    print(response.json())
else:
    print(f"Failed to update tag. Status code: {response.status_code}")
    print(response.json())
