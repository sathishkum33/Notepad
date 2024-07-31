from atlassian import Confluence
import pandas as pd

# Confluence instance details
confluence_url = "https://confluence.x.com"
api_token = "your_api_token"
username = "your_email@example.com"

# Initialize Confluence instance
confluence = Confluence(
    url=confluence_url,
    username=username,
    password=api_token
)

# Function to get page content
def get_page_content(page_id):
    page = confluence.get_page_by_id(page_id, expand='body.storage')
    return page['body']['storage']['value']

# Function to get all pages in a space
def get_pages_from_space(space_key):
    cql = f'space="{space_key}"'
    pages = confluence.cql(cql, expand='body.storage')
    return pages['results']

# Space key derived from your Confluence URL
space_key = "CIBCDM"

# Fetch pages
pages = get_pages_from_space(space_key)

# Prepare data
data = []
for page in pages:
    page_id = page['id']
    title = page['title']
    url = f"{confluence_url}/display/{space_key}/{title.replace(' ', '+')}"
    content = get_page_content(page_id)
    data.append({'title': title, 'url': url, 'content': content})

# Create DataFrame
df = pd.DataFrame(data)

# Save to CSV (optional)
df.to_csv('confluence_pages.csv', index=False)

print(df.head())