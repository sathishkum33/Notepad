import os
import zipfile
import requests

url = "your_zip_file_url_here"
target_directory = "/u01/app"
zip_filename = "downloaded_file.zip"
extracted_folder = "extracted_contents"

# Create target directory if it doesn't exist
if not os.path.exists(target_directory):
    os.makedirs(target_directory)

# Download the zip file
response = requests.get(url)
with open(os.path.join(target_directory, zip_filename), "wb") as zip_file:
    zip_file.write(response.content)

# Extract the contents of the zip file
with zipfile.ZipFile(os.path.join(target_directory, zip_filename), "r") as zip_ref:
    zip_ref.extractall(os.path.join(target_directory, extracted_folder))

print("Zip file downloaded and extracted successfully.")
