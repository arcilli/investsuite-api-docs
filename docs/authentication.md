---
title: Authentication
---

Authenticate against the API to receive a JSON Web Token (JWT). Learn more about JWT at [jwt.io](https://jwt.io). To authenticate you need an access key and a secret. Reach out to your InvestSuite representative or send a mail to [api@investsuite.com](mailto:api@investsuite.com) and we will set you up in no time.

!!! Warning
    Requests should not be directly sent from your app or website, as your authentication data may be exposed in transit. All requests are required to be made via an HTTPS connection; requests made over plain HTTP will fail.

When you successfully authenticate you receive an `access_token` and a `refresh_token`. Add the `access_token`to the HTTP headers in all subsequent requests. This is required to authenticate against the API service. You cannot access any endpoint without a valid JWT.

The `access_token` has a limited lifetime. The duration is added to the response body in the `expires_at`field, e.g `expires_at: 300`. Within the lifetime of the access_token you can refresh the token by issuing the `/auth/refresh-token` endpoint. We will try this out below where we will perform following three steps:

1. Login: Authenticate with `access_key_id` and `secret_access_key` to retrieve an access token.
2. Refresh token: Refresh the access token.
3. Add token: Issue a GET request with the access token.

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
    curl --location --request POST 'https://api.sandbox.investsuite.com/auth/login/' \
    --header 'Content-Type: application/json' \
    --data-raw '{
            "access_key_id":"{access_key}",
            "secret_access_key":"{secret}"
        }'
    ```

Response:
```JSON
{
    "access_token":"{access_token_string}",
    "token_type":"Bearer",
    "expires_in":"300",
    "refresh_token":"{refresh_token_string}"
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
        "refresh_token": "{refresh_token_string}"
    }

    ```

=== "curl"

    ```bash
    curl --location --request POST 'https://api.sandbox.investsuite.com/auth/refresh-token/' \
    --header 'Content-Type: application/json' \
    --data-raw '{"refresh_token": "{refresh_token_string}"}'
    ```
## Add token to requests

You are obliged to use the JWT Web Token in all subsequent requests. This is the token that is returned in the `access_token` property. API requests without authentication will fail and return a `403 Access Forbidden`. JSON Web Tokens must be specified via an authorization header as a Bearer token, eg: `Authorization: Bearer 4eC39HqLyjWDarjtT1zdp7dc`.

To try, replace {string} in the curl request below with the `access_token` you obtained above, and launch the command from your terminal.

=== "HTTP"

    ```HTTP
    GET /users/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Authorization: Bearer {access_token_string}
    ```

=== "curl"

    ```bash
    curl -X GET 'https://api.sandbox.investsuite.com/users/' \
    --H 'Authorization: Bearer {access_token_string}'
    ```
