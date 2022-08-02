---
title: X-Ray API
---

## Service Description

The X-Ray API provides a series of stock metrics. Our quantitative methodology scores stocks on 8 dimensions (Valuation, Growth, Momentum, Stability, Financial Health, Profitability, Size, Dividend Yield). 

To retrieve X-Ray perform a `GET` request against the `/xray/universe/` endpoint.

- [X-Ray API Redoc Documentation](https://api.data.uat.investsuite.com/redoc#tag/XRay)
- [X-Ray API Swagger UI Interface](https://api.data.uat.investsuite.com/docs#/XRay/)

## X-Ray GET
GET X-Rays for a tenant with at endpoint `/xray/universe/`.

=== "Curl Request"

    ```bash
    curl -X "GET" \
    "https://api.data.uat.investsuite.com/xray/universe/?fields={xray_fields}&normalization_universe={normalization_universe}&instrument_universe={instrument_universe}&group_split_type={group_split_type}&universe_name={universe_name}" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET"
    ```

=== "HTTP Request"

    ```HTTP
    GET /xray/universe/?fields={xray_fields}&normalization_universe={normalization_universe}&instrument_universe={instrument_universe}&group_split_type={group_split_type}&universe_name={universe_name} HTTP/1.1
    Host: api.data.uat.investsuite.com
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET
    accept: application/json
    Content-Type: application/json
    ````

Query Parameter Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`fields` | One or multiple X-Ray factors to retrieve.  | string | `VALUE_STARS` |  Yes
`normalization_universe` | The universe used to calculate ranked factors on.  | string | `GLOBAL` |  No
`instrument_universe` | The universe for which factors are calculated (which can be a subset of the normalization universe).  | string | `GLOBAL` |  No
`group_split_type` | How to group the universe for factors which depend on rankings within the universe.  | string | `REGION_SECTOR` |  No
`universe_name` | The universe to retrieve the factors for.  | string | `DEFAULT` |  Yes

After the request, we get the following example response:

=== "Response (Body Content JSON)"
  ```JSON
  [{
      "data": {
        "US88160R1014": {
            "VALUE_STARS": 1,
            "GROWTH_STARS": 4,
            ...
        },
        "US1912161007": {
            "VALUE_STARS": 4,
            "GROWTH_STARS": 1,
            ...
        },
        ...
      } 
    }
  ]
  ```
