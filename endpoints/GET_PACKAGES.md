Get Packages Endpoint
=====================
- Used to get packages that the user has access to.
- URL: `/`
- Http Method: `GET`
- Query Parameters:
    - `type` PackageType **Optional**: Allows you to filter on
    what type of package you wish to query for.
    Package types include:
      - All
      - My Package
      - Shared Package
    - `status` PackageStatus **Optional**: Allows you to filter on
    the status of package you wish to query for.
    Package statuses include:
      - Error Creating Package
      - Ready to Download
      - Download Paused
      - Download Complete
      - Package Deleted
      - Creating Package
      - Downloading
      - Pending
      - Download Stopped
      - Initiate
      - Download Error
      - Upload Complete
      - Error
      - Download Incomplete
      - Package Empty
      - GUID Web Service
    - `hasAssociatedFiles` Boolean **Optional**: Allows you to filter on
    if a package has files associated with it.
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
    "package_type" : "Shared Package",
    "permission_group" : "Permission Group",
    "file_count" : 2
  }
]
```