import requests
from requests.auth import HTTPBasicAuth
import json
from datetime import datetime

# Define variables
organization = "your_organization_name"
project = "your_project_name"
pat = "your_personal_access_token"  # Replace with your PAT
new_tag = "new-tag"  # Replace with the tag you want to add

# Get today's date in the format YYYY-MM-DD
today_date = datetime.today().strftime('%Y-%m-%d')

# Construct the WIQL query
wiql_query = {
    "query": f"SELECT [System.Id] FROM workitems WHERE [System.CreatedDate] = '{today_date}' AND [System.Tags] = ''"
}

# Construct the WIQL endpoint URL
wiql_url = f"https://dev.azure.com/{organization}/{project}/_apis/wit/wiql?api-version=6.0"

# Execute the WIQL query
response = requests.post(
    wiql_url,
    auth=HTTPBasicAuth('', pat),
    headers={'Content-Type': 'application/json'},
    data=json.dumps(wiql_query)
)

if response.status_code == 200:
    work_items = response.json().get('workItems', [])
    if not work_items:
        print("No work items found with empty tags created today.")
    else:
        print(f"Found {len(work_items)} work item(s) with empty tags created today.")
        for item in work_items:
            work_item_id = item['id']

            # Construct the PATCH request payload to update tags
            payload = [
                {
                    "op": "add",
                    "path": "/fields/System.Tags",
                    "value": new_tag
                }
            ]

            # Construct the work item endpoint URL
            work_item_url = f"https://dev.azure.com/{organization}/{project}/_apis/wit/workitems/{work_item_id}?api-version=6.0"

            # Update the work item with the new tag
            update_response = requests.patch(
                work_item_url,
                auth=HTTPBasicAuth('', pat),
                headers={'Content-Type': 'application/json-patch+json'},
                data=json.dumps(payload)
            )

            if update_response.status_code == 200:
                print(f"Successfully updated work item {work_item_id} with new tag.")
            else:
                print(f"Failed to update work item {work_item_id}. Status code: {update_response.status_code}")
                print(update_response.json())
else:
    print(f"Failed to execute WIQL query. Status code: {response.status_code}")
    print(response.json())
