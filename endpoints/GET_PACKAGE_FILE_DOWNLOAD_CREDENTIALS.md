Get Package File Download Credentials Endpoint
==============================================
- Used to generate file download AWS credentials. This
can be used to use AWS Libraries to download files from
the S3 bucket.
- URL: `/{packageId}/files/multiFileDownloadCredentials`
- Http Method: `GET`
- Query Parameters:
    - `package_file_id` Long[]: The file ids you want to
    generate an S3 download token for.
- [Requires authentication header](AUTHENTICATION.md)
- Return data:
```json
{
  "access_key" : "somekey",
  "secret_key" : "somesecretkey",
  "session_token" : "somesessiontoken",
  "expiration_date" : "2021-02-14T00:00:00.000-0500",
  "package_files" : [
    {
      "package_file_id" : 1,
      "destination_uri" : "s3://some/s3/url",
      "download_alias" : "filename"
    }
  ]
}
```