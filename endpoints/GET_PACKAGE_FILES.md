Get Package Files Endpoint
==========================
- Used to get the files of a specific package that the
user has access to.
- URL: `/{packageId}/files`
- Http Method: `GET`
- URL Parameters:
    - `packageId` Long: The id of the package
    you want to fetch the files for.
- Query Parameters:
    - `types` FileType[] **Optional**: An array of the desired
    file types. File types include:
        - Package Metadata
        - Data
        - Study
        - Experiment
        - Associated
        - Collection
        - Unknown
    - `page` Integer **Optional**: Results are paginated to make
    processing easier, this value determines which
    page to query. Can also be `last` or `first`.
    - `size` Integer **Optional**: Determines the size of the
    pages returned. Can also be `all`.
- [Requires authentication header](AUTHENTICATION.md)
- Return data:
```json
{
  "results" : [
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
  ],
  "_links" : {
    "next" : {
      "href" : "/{packageId}/files?page=2&size=1"
    },
    "previous" : {
      "href" : "/{packageId}/files?page=1&size=1"
    },
    "first" : {
      "href" : "/{packageId}/files?page=1&size=1"
    },
    "last" : {
      "href" : "/{packageId}/files?page=15&size=1"
    }
  }
}
```