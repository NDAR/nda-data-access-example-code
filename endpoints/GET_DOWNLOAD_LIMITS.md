Get Download Limits Endpoint
============================
- Used to get the download limits of the user.
- URL: `/downloadlimits`
- Http Method: `GET`
- No Parameters
- [Requires authentication header](AUTHENTICATION.md)
- Return data:
```json
{
  "download_threshold" : 21990232555520,
  "download_volume_outside_aws" : 1069080920055,
  "download_volume_inside_aws" : 2208240775095,
  "download_volume_monthly_outside_aws" : 32858087790,
  "download_volume_monthly_inside_aws" : 321444489496
}
```