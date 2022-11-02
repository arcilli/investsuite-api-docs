---
title: Reference API
---

## Service Description

Query reference data fields for financial instruments such as ISIN, asset class, market cap, etc.
User-defined data with custom fields can be uploaded with the [Custom Reference Data endpoint](../insight-apis/custom_data.md).

- [Reference Data Model Docs](https://api.data.investsuite.com/redoc#operation/reference_query_data_reference_query__post)
- [Reference Data UI Interface](https://api.data.investsuite.com/docs#/Financial%20Data/reference_query_data_reference_query__post)

## Reference Query POST
Use this POST endpoint to query reference data fields for a list of instruments.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.investsuite.com/data/reference/query/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{
      "instrument_ids": [
        "BE0974293251",
        "BE0003851681"
        ],
      "fields": [
        "ASSET_CLASS",
        "CURRENCY"
        ],
      "fields_extra": []
    }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/reference/query/ HTTP/1.1
    Host: api.data.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET
    ```

Field | Type | Description | Data type | Example | Required
----- | ---- | ----------- | --------- | ------- | --------
`accept` | Request header parameter | `application/json` returns the reference results as a json object. `application/octet-stream` as a binary Python pandas dataframe. | str |  | Yes, default `application/json`
`instrument_ids` | Request body JSON data | List of instrument identifiers for which to request data fields. | `list[str]` | cf. above | Yes
`fields` | Request body JSON data | List of instrument identifiers for which to request data fields. Available data fields can be seen in the drop-down at [API documentation of this endpoint](https://api.data.investsuite.com/redoc#operation/reference_query_data_reference_query__post)| `list[str]` | cf. above | Yes
`fields_extra` | Request body JSON data | List of instrument field keys which are custom / user-defined and uploaded in the custom endpoint. | `list[str]` | cf. above | Yes

After the request, we get the following example response with the data field values per instrument_id:

=== "Response (Body Content JSON)"

    ```JSON
    {
       "data":{
          "BE0003851681":{
             "asset_class":"EQUITY",
             "currency":"USD"
          },
          "BE0003851681":{
             "asset_class":"EQUITY",
             "currency":"USD"
          }
       },
       "meta":null
    }
    ```

## Reference Universe GET
Use this GET endpoint to get reference data fields for the instruments in the tenant's universe.

=== "Curl Request"

    ```bash
    curl -X "GET" \
    "https://api.data.investsuite.com/data/reference/universe/?fields=ASSET_CLASS&fields=CURRENCY" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET"
    ```

=== "HTTP Request"

    ```HTTP
    GET /data/reference/universe/ HTTP/1.1
    Host: api.data.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET
    ```

Field | Type | Description | Data type | Example | Required
----- | ---- | ----------- | --------- | ------- | --------
`accept` | Request Header parameter | `application/json` returns the reference results as a json object. `application/octet-stream` as a binary Python pandas dataframe. | str |  | Yes, default `application/json`
`universe_name` | Request query parameter | The named universe for which to return that universe instruments' reference data. Tenants have universes for different use-cases, cf. [Tenant Configuration](../insight-apis/tenant_config.md) | `str` | `ROBO` | No, default returns union set of instruments of all universes of the tenant.
`fields` | Request query parameter | List of instrument identifiers for which to request data fields. Available data fields can be seen in the drop-down at [API documentation of this endpoint](https://api.data.investsuite.com/redoc#operation/reference_querTODO: Assign ReferenceData model to response output so I can link redoc model docs here.y_data_reference_query__post)| `list[str]` | cf. above | Yes
`fields_extra` | Request body JSON data | List of instrument field keys which are custom / user-defined and uploaded in the custom endpoint. | `list[str]` | cf. above | Yes

After the request, we get the following example response with the data field values for all instruments in the tenant universe:

=== "Response (Body Content JSON)"

    ```JSON
    {
       "data":{
          "BE0003851681":{
             "asset_class":"EQUITY",
             "currency":"EUR"
          },
          ...
          "BE0974293251":{
             "asset_class":"EQUITY",
             "currency":"EUR"
          }
       },
       "meta":null
    }
    ```

## Custom Reference POST
See [Custom Reference Data endpoint](../insight-apis/custom_data.md)


## FAQ
<details>
<summary>Where do I find the return type of a field?</summary>
TODO: Assign ReferenceData model to response output so I can link redoc model docs here.
</details>
>
