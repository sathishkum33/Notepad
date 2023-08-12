import requests

artifactory_url = 'https://your-artifactory-url/artifactory'
repository = 'your-repository'
directory_path = 'path/to/your/directory'

api_url = f'{artifactory_url}/{repository}/{directory_path}?list'

response = requests.get(api_url, auth=('your-username', 'your-api-key'))

if response.status_code == 200:
    directory_data = response.json()
    for item in directory_data['files']:
        print(item['uri'])
else:
    print("Error fetching directory contents:", response.status_code)
