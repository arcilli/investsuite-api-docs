---
title: Authentication
---

Authenticate against the API to receive a JSON Web Token (JWT). Learn more about JWT at [jwt.io](https://jwt.io). To authenticate you need an access key and a secret. Reach out to your InvestSuite representative and we will set you up in no time.

!!! Warning
    Requests should not be directly sent from your app or website, as your authentication data may be exposed in transit. All requests are required to be made via an HTTPS connection; requests made over plain HTTP will fail.

When you successfully authenticate you receive an `access_token` and a `refresh_token`. Add the `access_token`to the HTTP headers in all subsequent requests. The `access_token` has a limited lifetime. The duration is added to the response body in the `expires_at`field, e.g `expires_at: 300`. 

## Login

=== "HTTP"

    ```HTTP 
    POST /auth/login/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Content-Type: application/json

    {
        "access_key_id": "{access_key}",
        "secret_access_key": "{secret}"
    }

    ```

=== "curl"

    ```bash
    curl -X POST \                 
    -H "Content-Type: application/json" \
    -d '{"access_key_id":"nd348h88yhGNUHhhhb78y6gj","password":"secret"}' \
    https://api.sandbox.investsuite.com/auth/login/
    ```

Response:
```JSON
{
    "access_token":"eyJ0eXAiOiJKV1QiL...",
    "token_type":"Bearer",
    "expires_in":"300",
    "refresh_token":"eyJraWQiOiJEb..."
}
```

## Refresh token

Use the `/auth/refresh-token/`endpoint to silently prolong the session. This endpoint will return the same response as `/auth/login` does.

=== "HTTP"

    ```HTTP 
    POST /auth/refresh-token/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Content-Type: application/json

    {
        "refresh_token": "{string}"
    }

    ```

=== "curl"

    ```bash
    curl -X POST \                 
    -H "Content-Type: application/json" \
    -d '{"refresh_token": "{string}"}' \
    https://api.sandbox.investsuite.com/auth/refresh-token/
    ```







