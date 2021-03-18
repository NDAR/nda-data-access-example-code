Get Package Files From S3 Endpoint
==================================
- Used to convert S3 links to package file references.
- URL: `/{packageId}/files`
- Http Method: `POST`
- URL Parameters:
    - `packageId` Long: The id of the package
      you want to fetch the files for.
- No Query Parameters
- Post Parameters:
    - String[]: The json post data to this endpoint
      should only contain an array of strings that are
      the s3 urls to convert to Package Service files.
- Sample Post Data:
```json
[
  "s3://some/s3/url",
  "s3://some/other/s3/url"
]
```
- [Requires authentication header](AUTHENTICATION.md)
- Return data:
```json
[
  {
    "dataFile" : false,
    "documentFile" : false,
    "associatedFile" : false,
    "_links" : {
      "self" : {
        "href" : "/{packageId}/files/1"
      },
      "download_url" : {
        "href" : "/{packageId}/files/1/download_url"
      },
      "download_token" : {
        "href" : "/{packageId}/files/1/download_token"
      }
    },
    "package_file_id" : 1,
    "download_alias" : "somefile.pdf",
    "file_size" : 2048,
    "is_associated_file" : false,
    "created_date" : "2021-02-10T00:00:00.000-0500",
    "is_data_file" : false,
    "is_document_file" : false,
    "nda_file_type" : "Package Metadata"
  }
]
```