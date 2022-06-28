---
title: Endpoint Name Title
---

## Service Description

Describe the service here in natural language and provide documentation and UI link:

- [Themes API Documentation](https://api.data.uat.investsuite.com/redoc#tag/{})
- [Themes API Swagger UI Interface](https://api.data.uat.investsuite.com/docs#/Themes/)


## Usage & Model

A subsection per request type and subpath in the endpoint.

### GET example

=== "Curl Request"

    ```bash
    curl -X "GET" \
    "https://api.data.uat.investsuite.com/$ENDPOINT_NAME/?$EXAMPLE_QUERY_PARAMS" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    ```

=== "HTTP Request"

    ```HTTP
    GET /{endpoint-name}/?{example-query-params} HTTP/1.1
    Host: api.data.uat.investsuite.com
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET
    accept: application/json
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
