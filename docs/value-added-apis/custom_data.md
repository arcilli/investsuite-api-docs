---
title: Uploading Custom Data 
---

## Service Description

InvestSuite offers a range of products, all using financial data. Each product has access a few to market data providers. Sometimes the needed data is not available with these providers, or a client may want to upload their own data to guarantee that exactly the same numbers as they report are shown in the InvestSuite products. In that case, the client will upload the custom data to InvestSuite via the API endpoints as described on this page.  

The Financial Data API accepts custom data via four different endpoints, each accepting specific data: 

- [Reference data](https://api.data.dev.investsuite.com/redoc#operation/create_reference_batch_data_custom_reference_batch__post)
- [Timeseries data](https://api.data.dev.investsuite.com/redoc#operation/create_timeseries_batch_data_custom_timeseries_batch__post)
- [Composition data](https://api.data.dev.investsuite.com/redoc#operation/create_composition_batch_data_custom_composition_batch__post)
- [Attribution data](https://api.data.dev.investsuite.com/redoc#operation/create_attribution_batch_data_custom_attribution_batch__post)

The client will most likely use a combination of these.


## Uploading Custom Reference Data

Reference Data consists of instrument-level data of which only the latest value is ever relevant. Examples of reference data are the ISIN code, asset class, ticker, name, and instrument type. Using this endpoint, a client can upload reference data for specific instruments. 

The endpoint accepts a batch of instruments at once, and has a range of predefined fields. Let us look at an example.


=== "HTTP"

    ```HTTP 
    POST /data/custom/reference/batch/?X-TENANT-ID={your_identifier} HTTP/1.1
    Host: api.data.uat.investsuite.com
    Content-Type: application/json
    Authorization: Bearer {access_token_string}

    {
        "data": [
            {
                "instrument_id": "US0378331005",
                "reference_data": {
                    "country": "US",
                    "name": "APPLE",
                    "roundlot_size": 1
                }
            },
            {
                "instrument_id": "US2546871060",
                "reference_data": {
                    "country": "US",
                    "name": "WALT DISNEY",
                    "roundlot_size": 1
                }
            }
        ]
    }

    ```

=== "curl"

    ```bash
    curl -X POST \                 
    -H "Content-Type: application/json" \
    -H "Auhorization": "{string}"  \   
    -d '{
            "data": [
                {
                    "instrument_id": "US0378331005",
                    "reference_data": {
                        "country": "US",
                        "name": "APPLE",
                        "roundlot_size": 1
                    }
                },
                {
                    "instrument_id": "US2546871060",
                    "reference_data": {
                        "country": "US",
                        "name": "WALT DISNEY",
                        "roundlot_size": 1
                    }
                }
            ]
        }'
    https://api.data.uat.investsuite.com/data/custom/reference/batch/?X-TENANT-ID={your_identifier}
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | A list that holds a reference data object for each provided instrument. | `list` |  | yes 
`instrument_id` | The ID of the instrument. This will be used by all InvestSuite products to identify the instrument. While the API does not impose any restrictions on the format, some InvestSuite products do. Make sure to check these restrictions for the products that you will be using before uploading data. | `string` | "US2546871060" | yes
`reference_data` | An object holding the actual reference data of the instrument. Available data fields can be seen in the [API documentation of this endpoint](https://api.data.dev.investsuite.com/redoc#operation/create_reference_batch_data_custom_reference_batch__post). | `object` | | yes

After uploading data, we get a response back: 

```JSON
{
  "data": {
    "cache_instrument_count": 2,
    "cache_field_count": 3
  }
}
```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | Holds information about the current database. | `object` |  | yes 
`cache_instrument_count` | The number of instruments that the client has provided custom reference data for. | `integer` | 2 | yes
`cache_field_count` | The number of different fields that the client has provided reference data for, over all instruments. | `integer` | 3 | yes

To overwrite the data of an instrument, simply provide the reference data fields that need to be overwritten again for that instrument. 

## Uploading Custom Timeseries Data

Timeseries Data consist of instrument-level data for which data changes frequently (usually daily) and for which historical values are relevant. Examples of timeseries data are NAV, adjusted price, yield, and interest rate. Using this endpoint, a client can upload timeseries data for specific instruments, and for a specific type. 

The endpoint accepts a batch of instruments at once, for the same type of timeseries data. Let us look at an example.

=== "HTTP"

    ```HTTP 
    POST /data/custom/timeseries/batch/?X-TENANT-ID={your_identifier} HTTP/1.1
    Host: api.data.uat.investsuite.com
    Content-Type: application/json
    Authorization: Bearer {access_token_string}

    {
        "data": [
            {
                "instrument_id": "US0378331005",
                "values": {
                    "2022-03-07": 158834,
                    "2022-03-08": 156979.4,
                    "2022-03-09": 162473.3,
                    "2022-03-10": 158056.3,
                    "2022-03-11": 154277.3,
                    "2022-03-14": 150179.4
                }
            },
            {
                "instrument_id": "US2546871060",
                "values": {
                    "2022-03-07": 9615.215,
                    "2022-03-08": 9489.172,
                    "2022-03-09": 9626.016,
                    "2022-03-10": 9625.297,
                    "2022-03-11": 9489.172,
                    "2022-03-14": 9293.266
                }
            }
        ],
        "timeseries_type": "ADJUSTED_PRICE"
    }

    ```

=== "curl"

    ```bash
    curl -X POST \                 
    -H "Content-Type: application/json" \
    -H "Auhorization": "{string}"  \   
    -d '{
            "data": [
                {
                    "instrument_id": "US0378331005",
                    "values": {
                        "2022-03-07": 158834,
                        "2022-03-08": 156979.4,
                        "2022-03-09": 162473.3,
                        "2022-03-10": 158056.3,
                        "2022-03-11": 154277.3,
                        "2022-03-14": 150179.4
                    }
                },
                {
                    "instrument_id": "US2546871060",
                    "values": {
                        "2022-03-07": 9615.215,
                        "2022-03-08": 9489.172,
                        "2022-03-09": 9626.016,
                        "2022-03-10": 9625.297,
                        "2022-03-11": 9489.172,
                        "2022-03-14": 9293.266
                    }
                }
            ],
            "timeseries_type": "ADJUSTED_PRICE"
        }'
    https://api.data.uat.investsuite.com/data/custom/timeseries/batch/?X-TENANT-ID={your_identifier}
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | A list that holds a timeseries data object for each provided instrument. | `list` |  | yes 
`instrument_id` | The ID of the instrument. This will be used by all InvestSuite products to identify the instrument. While the API does not impose any restrictions on the format, some InvestSuite products do. Make sure to check these restrictions with the products that you will be using before uploading data. | `string` | "US2546871060" | yes
`values` | An object holding the timeseries data for the instrument. | `object` | | yes
`timeseries_type` | The type of timeseries data that is provided. Available types can be seen in the [API documentation of this endpoint](https://api.data.dev.investsuite.com/redoc#operation/create_timeseries_batch_data_custom_timeseries_batch__post)


After uploading data, we get a response back: 

```JSON
{
  "data": {
    "cache_instrument_count": 2,
    "cache_date_count": 6
  }
}
```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | Holds information about the current database. | `object` |  | yes 
`cache_instrument_count` | The number of instruments that the client has provided custom timeseries data for. | `integer` | 2 | yes
`cache_date_count` | The number of different dates that the client has provided timeseries data for, over all instruments. | `integer` | 3 | yes

To overwrite a certain type of timeseries data for one or more instruments on specific dates, simply provide the data for these instruments on the dates to overwrite again. 