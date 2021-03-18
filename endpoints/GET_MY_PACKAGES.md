Get My Packages Endpoint
========================
- Gets the packages created by your user, similar to
[Get Packages](GET_PACKAGES.md) but executes a less expensive query.
- URL: `/mypackages`
- Http Method: `GET`
- No Parameters
- [Requires authentication header](AUTHENTICATION.md)
- Return data:
```json
[
  {
    "links" : [
      {
        "rel" : "self",
        "href" : "https://nda.nih.gov/api/package/1"
      },
      {
        "rel" : "associate",
        "href" : "https://nda.nih.gov/api/package/1/associate"
      }
    ],
    "package_id" : 1,
    "status" : "Ready to Download",
    "description" : "Description",
    "total_package_size" : 1024,
    "has_associated_files" : true,
    "created_date" : "2021-02-11T00:00:00.000-0500",
    "package_type" : "My Package",
    "permission_group" : "Permission Group",
    "file_count" : 2
  }
]
```