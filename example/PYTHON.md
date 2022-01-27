Python Code Example
===================
Some sample python code for retrieving files
from Package Service. The `requests` library
is used in this example (`pip install requests`).
The sample code was written using Python v3.9.1.

Authentication
--------------

You can verify if your authentication credentials work
by using the `/auth` endpoint:
```python
import base64
import requests
import json
import urllib.request
import shutil
from pathlib import Path

# Encode our credentials then convert it to a string.
credentials = base64.b64encode(b'username:password').decode('utf-8')

# Create the headers we will be using for all requests.
headers = {
    'Authorization': 'Basic ' + credentials,
    'User-Agent': 'Example Client',
    'Accept': 'application/json'
}

# Send Http request
response = requests.get('https://nda.nih.gov/api/package/auth', headers=headers)

# Business Logic.

# If the response status code does not equal 200
# throw an exception up.
if response.status_code != requests.codes.ok:
    print('failed to authenticate')
    response.raise_for_status()

# The auth endpoint does no return any data to parse
# only a Http response code is returned.
```

The auth endpoint is not required in your usage though,
it is only helpful for validating your credentials.

Retrieving Files
----------------
#### From Nothing:
If you want to get and download all files related to a
single package:
```python
# Assume code in authentication section is present.

packageId = 1234

# Construct the request to get the files of package 1234
# URL structure is: https://nda.nih.gov/api/package/{packageId}/files
response = requests.get('https://nda.nih.gov/api/package/' + str(packageId) + '/files', headers=headers)

# Get the results array from the json response.
results = response.json()['results']

# Business Logic.

files = {}

# Add important file data to the files dictionary.
for f in results:
    files[f['package_file_id']] = {'name': f['download_alias']}
```

#### From S3 URL
This is a use-case that was inspired by
the [DCAN Labs NDA ABCD Downloader](https://github.com/DCAN-Labs/nda-abcd-s3-downloader).
([More on this workflow](../MANIFEST_FILE_NOTE.md))
This method allows you to convert pre-existing s3
file references to Package Service files:
```python
import csv

# Assume code in authentication section is present.

packageId = 1234

s3Files = []

# Load in and process the manifest file.
# Not all manifest files are structured like this, all you require is
# an S3 url and a package that has the files associated with it.
with open('datastructure_manifest.txt', 'r') as manifest:
    for rows in csv.reader(manifest, dialect='excel-tab'):
        for row in rows:
            if row.startsWith('s3://'):
                s3Files.append(row)

# The manifest files have their column declarations listed twice, trim those out
s3Files = s3Files[2:]

# Construct the request to get the files of package 1234
# URL structure is: https://nda.nih.gov/api/package/{packageId}/files
response = requests.post('https://nda.nih.gov/api/package/' + str(packageId) + '/files', json=s3Files, headers=headers)

# Business Logic.

files = {}

# Add important file data to the files dictionary.
# We can skip having to transform the json because a json array is returned.
for f in response.json():
    files[f['package_file_id']] = {'name': f['download_alias']}
```

Downloading Files
-----------------
After receiving files using one of the above methods you will have a dictionary of
all file ids in a package and their names. If you were to want to then move
to downloading said files, you'd do something along the lines of:
```python
# Assume code in authentication section is present.
# Assume that one of the retrieving files implementations is present too

# Create a post request to the batch generate presigned urls endpoint.
# Use keys from files dictionary to form a list, which is converted to
# a json array which is posted.
response = requests.post('https://nda.nih.gov/api/package/' + str(packageId) + '/files/batchGeneratePresignedUrls', json=list(files.keys()), headers=headers)

# Get the presigned urls from the response.
results = response.json()['presignedUrls']

# Business Logic.

# Add a download key to the file's data.
for url in results:
    files[url['package_file_id']]['download'] = url['downloadURL']

# Iterate on file id and it's data to perform the downloads.
for id, data in files.items():
    name = data['name']
    downloadUrl = data['download']
    # Create a downloads directory
    file = 'downloads/' + name
    # Strip out the file's name for creating non-existent directories
    directory = file[:file.rfind('/')]
    
    # Create non-existent directories, package files have their
    # own directory structure, and this will ensure that it is
    # kept in tact when downloading.
    Path(directory).mkdir(parents=True, exist_ok=True)
    
    # Initiate the download.
    with urllib.request.urlopen(downloadUrl) as dl, open(file, 'wb') as out_file:
        shutil.copyfileobj(dl, out_file)
```

Endpoints used in this example:
- [Authentication](../endpoints/AUTHENTICATION.md)
- [Get Package Files](../endpoints/GET_PACKAGE_FILES.md)
- [Batch Generate Presigned URLS](../endpoints/BATCH_GENERATE_PRESIGNED_URLS.md)
- [Get Package Files from S3 Link](../endpoints/GET_PACKAGE_FILES_FROM_S3.md)
