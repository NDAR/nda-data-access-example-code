Get Shared Packages Endpoint
============================
- Gets the packages shared with your user, similar to
  [Get Packages](GET_PACKAGES.md) but executes a less expensive query.
- URL: `/sharedpackages`
- Http Method: `GET`
- No Parameters
- [Requires authentication header](AUTHENTICATION.md)
- Return data:
```json
[
  {
    "userId": 1,
    "dataRepositoryId": 1,
    "dataRepositoryName": "Name",
    "dataRepositoryDesc": "Description",
    "packageId": 1,
    "packageName": "PackageName",
    "packageDescription": "Package Description",
    "createdDate": "2021-02-03T00:11:57.095-0500",
    "fileCount": 1,
    "fileSize": 1024,
    "links": [
      {
        "rel": "self",
        "href": "https://nda.nih.gov/api/package/1"
      },
      {
        "rel": "associate",
        "href": "https://nda.nih.gov/api/package/1/associate"
      }
    ]
  }
]
```