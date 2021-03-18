Authentication Endpoint
=======================
- Will return a 200 if user credentials are valid and usable.
- URL `/auth`
- Http Method: `GET`
- No Parameters
- Requires authentication header
    - `Authentication`: `Basic <base64 encoded username:password>`
- No return data.