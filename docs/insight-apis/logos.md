---
title: Logos API
---

## Service Description

Get a company logo image by an instrument identifier (ISIN).

- [Logos API Documentation](https://api.data.investsuite.com/redoc#tag/Logos)
- [Logos API Swagger UI Interface](https://api.data.investsuite.com/docs#/Logos/)

Our logo database is automatically collected with human-in-the-loop verification and periodic updates to catch out-of-date assets.

## Logo GET

Request a logo for an ISIN as .png image of a certain size with 1:1 square aspect ratio.

=== "Curl Request"

    ```bash
    curl -X "GET" \
    "https://api.data.investsuite.com/logo/?isin=BE0974293251&use_only_cache=true&size=128" \
    -H "accept: */*" \
    -H "Content-Type: image/png" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    ```

=== "HTTP Request"

    ```HTTP
    GET /logo/?isin=BE0974293251&use_only_cache=true&size=128 HTTP/1.1
    Host: api.data.investsuite.com
    accept: */*
    Content-Type: image/png
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`isin` | The ISIN identifier of the company. | `str` | `BE0974293251` | Yes
`use_only_cache` | Set to `false`, to bypass the cache. Cached requests are recommended. | `bool: {true|false}` | `true` | No, default `true`
`size` | Size in pixels from 0 up to 1000. All returned images have square aspect ratio 1:1. | `int` | No, default to original asset size.

After the request, we get the following example response:

=== "Response (Body Image)"
The image binary in `image/png` format.
