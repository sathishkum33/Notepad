import requests
import pandas as pd
from requests.auth import HTTPBasicAuth

# Replace with your Confluence site and API token
confluence_base_url = "https://your-confluence-site.atlassian.net/wiki/rest/api"
api_token = "your_api_token"
username = "your_email@example.com"

# Set up authentication
auth = HTTPBasicAuth(username, api_token)

# Function to get page content
def get_page_content(page_id):
    url = f"{confluence_base_url}/content/{page_id}?expand=body.storage"
    response = requests.get(url, auth=auth)
    response.raise_for_status()
    content = response.json()
    return content['body']['storage']['value']

# Function to get all pages in a space
def get_pages_from_space(space_key):
    url = f"{confluence_base_url}/content?spaceKey={space_key}&expand=body.storage"
    response = requests.get(url, auth=auth)
    response.raise_for_status()
    pages = response.json()['results']
    return pages

# Example space key
space_key = "YOUR_SPACE_KEY"

# Fetch pages
pages = get_pages_from_space(space_key)

# Prepare data
data = []
for page in pages:
    page_id = page['id']
    title = page['title']
    url = f"{confluence_base_url}/content/{page_id}"
    content = get_page_content(page_id)
    data.append({'title': title, 'url': url, 'content': content})

# Create DataFrame
df = pd.DataFrame(data)

# Save to CSV (optional)
df.to_csv('confluence_pages.csv', index=False)

print(df.head())