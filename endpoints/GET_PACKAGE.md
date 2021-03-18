Get Package Endpoint
====================
- Used to get data on a specific package.
- URL: `/{packageId}`
- Http Method: `GET`
- URL Parameters:
    - `packageId` Long: The id of the package
      you want to fetch the files for.
- [Requires authentication header](AUTHENTICATION.md)
- Return data:
```json
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
```