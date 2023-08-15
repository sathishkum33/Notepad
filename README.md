import re

# Read the content of the script list file
with open('script_list.txt', 'r') as file:
    script_list_content = file.readlines()

# Extract filenames meeting the conditions
filenames = []
for line in script_list_content:
    # Check if the line contains '\i', doesn't contain '--', and ends with '.sql'
    if '\i' in line and '--' not in line and line.strip().endswith('.sql'):
        # Extract the filename after '\i'
        filename = re.search(r'\b\i\s+(\S+\.sql)', line).group(1)
        filenames.append(filename)

print(filenames)
