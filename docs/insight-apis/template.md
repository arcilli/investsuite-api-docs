---
title: Endpoint Name Title
---

## Service Description

Describe the service here in natural language and provide documentation and UI link:

- [Themes API Documentation](https://api.data.investsuite.com/redoc#tag/{})
- [Themes API Swagger UI Interface](https://api.data.investsuite.com/docs#/Themes/)

## GET example

=== "Curl Request"

    ```bash
    curl -X "GET" \
    "https://api.data.investsuite.com/$ENDPOINT_NAME/?$EXAMPLE_QUERY_PARAMS" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    ```

=== "HTTP Request"

    ```HTTP
    GET /$ENDPOINT_NAME/?$EXAMPLE_QUERY_PARAMS HTTP/1.1
    Host: api.data.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`{example-query-params}` | Description. | data type: `possible|values` | Example | Yes/No, default `false`


After the request, we get the following example response:

=== "Response (Body Content JSON)"

    ```JSON
    [
      {"example-field": "example_data"},
      {"example-field": "example_data"},
      ...  # truncate long responses of the same values for readability
      {"example-field": "example_data"},
      {"example-field": "example_data"},
    ]
    ```

### Exceptions
