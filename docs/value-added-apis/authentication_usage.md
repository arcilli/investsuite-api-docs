---
title: Authentication and Usage
---

## Authentication

To authenticate you need a tenant-user identifier and a secret key.
Authenticate in the request header with your tenant-user identifier in the `X-TENANT-ID` header field and your secret key in the `X-Api-Key` header field. Reach out to your InvestSuite representative or send a mail to [api@investsuite.com](mailto:api@investsuite.com) and we will set you up in no time.

Example requests with authentication headers:

=== "Curl Request"

    ```bash
    curl -X "GET" \
    "https://api.data.investsuite.com/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET"
    ```

=== "HTTP Request"

    ```HTTP
    GET / HTTP/1.1
    Host: api.data.investsuite.com
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET
    accept: application/json
    ```

!!! Warning
    Requests should not be directly sent from your app or website, as your authentication data may be exposed in transit. All requests are required to be made via an HTTPS connection; requests made over plain HTTP will fail.


## API Docsite Usage
Auto-generated documentation showing REST OpenAPI data models and response descriptions at the `redoc` path: [https://api.data.investsuite.com/redoc](https://api.data.investsuite.com/redoc)

You can navigate the data models and schemata by clicking the drop-down buttons for the relevant field.


## Webinterface Usage
An interactive Swagger UI interface is available for testing at the `/docs` path: [https://api.data.investsuite.com/docs](https://api.data.investsuite.com/redoc)

**⚠️ `/docs` Interactive Swagger UI caching issues**:

Browsers will sometimes cache request-responses returning stale/previous responses. This is due to our caching header parameters.
Please clear your browser cache (`Ctrl + Shift + Delete` in most browser) to resolve these issues.
