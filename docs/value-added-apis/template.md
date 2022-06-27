---
title: endpoint-name Title
---

## Service Description

Describe the service here in natural language and provide documentation and UI link:

- [Themes API Documentation](https://api.data.uat.investsuite.com/redoc#tag/{})
- [Themes API Swagger UI Interface](https://api.data.uat.investsuite.com/docs#/Themes/)


## Usage & Model

A subsection per request type and subpath in the endpoint.

### GET example

=== "HTTP Request"

    ```HTTP
    GET /{endpoint-name}/?{example-query-params} HTTP/1.1
    Host: api.data.uat.investsuite.com
    X-TENANT-ID: {your-tenant-identifier}
    X-Api-Key: {YOUR_API_SECRET_KEY}
    accept: application/json
    ```

=== "Curl Request"

    ```bash
    curl -X 'GET' \
    'https://api.data.uat.investsuite.com/{endpoint-name}/?{example-query-params}' \
    -H 'X-TENANT-ID: {your-tenant-identifier}' \
    -H 'X-Api-Key: {YOUR_API_SECRET_KEY}' \
    -H 'accept: application/json'
    ```

Query Parameter Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`{example-query-params}` | Description. | data type: `possible|values` | Example | Yes/No, default `false`


After the request, we get the following example response:

=== "HTTP Response (body content)"

    ```JSON
    [
      {'example-field': 'example_data'},
      {'example-field': 'example_data'},
      ...  # truncate long responses of the same values for readability
      {'example-field': 'example_data'},
      {'example-field': 'example_data'},
    ]
    ```
