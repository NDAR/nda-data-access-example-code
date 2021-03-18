Batch Generate Presigned Urls Endpoint
======================================
- This endpoint is used to generate presigned urls to
allow for the downloading of files from the Package Service.
- URL: `/{packageId}/files/batchGeneratePresignedUrls`
- Http Method: `POST`
- URL Parameters:
    - `packageId` Long: The id of the package
      you want to fetch the files for.
- No Query Parameters
- Post Parameters:
    - Long[]: The json post data to this endpoint
    should only contain an array of longs that are
    the file ids to generate urls for.
- Sample Post Data:
```json
[
  1,
  2
]
```
- [Requires authentication header](AUTHENTICATION.md)
- Return data:
```json
{
  "presignedUrls" : [
    {
      "package_file_id" : 1,
      "downloadURL" : "https://somes3url.s3.amazonaws.com"
    },
    {
      "package_file_id" : 2,
      "downloadURL" : "https://somes3url.s3.amazonaws.com"
    }
  ],
  "errors" : []
}
```