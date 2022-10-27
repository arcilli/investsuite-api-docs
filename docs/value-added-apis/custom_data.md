---
title: Custom Data API
---

## Service Description

InvestSuite offers a range of products, all using financial data. Each product has access a few to market data providers. Sometimes the necessary data is not available with these providers, or a client may want to upload their own data to guarantee exactly the same numbers in the InvestSuite products as they report themselves. In that case, the client will upload the custom data to InvestSuite via the API endpoints as described on this page. This then takes precedence on the data from the provider.

The Financial Data API accepts custom data via several endpoints, each accepting a specific type of data:

- [Custom Reference Model Docs](https://api.data.uat.investsuite.com/redoc#tag/Custom-Reference)
- [Custom Timeseries Model Docs](https://api.data.uat.investsuite.com/redoc#tag/Custom-Timeseries)
- [Custom Composition Model Docs](https://api.data.uat.investsuite.com/redoc#tag/Custom-Composition)
- [Custom Composition Timeseries Model Docs](https://api.data.uat.investsuite.com/redoc#tag/Custom-Composition-Timeseries)
- [Custom Attribution Model Docs](https://api.data.uat.investsuite.com/redoc#tag/Custom-Attribution)

The client will most likely use a combination of these endpoints. Below, we elaborate further on how to use these endpoints in practice.


## Custom Reference

### POST

Reference data consists of instrument-level data of which only the latest value is relevant. Examples of reference data are the ISIN code, asset class, ticker, name, and instrument type. Using this endpoint, a client can upload reference data for specific instruments.

The endpoint accepts a batch of instruments at once, and has a range of predefined fields to upload data.

**Overwriting existing data:** Updating can be done by providing new data values at the instrument/field-level.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/reference/batch/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{
            "data": [
                {
                    "instrument_id": "BE0974293251",
                    "reference_data": {
                        "country": "BE",
                        "name": "Ab InBev SA-NV",
                        "roundlot_size": 1
                    }
                },
                {
                    "instrument_id": "BE0003851681",
                    "reference_data": {
                        "country": "BE",
                        "name": "Aedifica",
                        "roundlot_size": 1
                    }
                }
            ]
        }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/reference/batch/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
        "data": [
            {
              "instrument_id": "BE0974293251",
              "reference_data": {
                  "country": "BE",
                  "name": "Ab InBev SA-NV",
                  "roundlot_size": 1
              }
            },
            {
              "instrument_id": "BE0003851681",
              "reference_data": {
                  "country": "BE",
                  "name": "Aedifica",
                  "roundlot_size": 1
                }
            }
        ]
    }
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | A list that holds a reference data object for each provided instrument. | `list` |  | yes
`instrument_id` | The ID of the instrument. This will be used by InvestSuite products to identify the instrument. While the API does not impose any restrictions on the format, but some InvestSuite products do. Make sure to check these restrictions with the products that you will be using before uploading data. **There is not necessarily a relationship to the ISIN.**| `string` | "BE0974293251" | yes
`reference_data` | An object holding the actual reference data of the instrument. Available data fields can be seen in the drop-down at [API documentation of this endpoint](https://api.data.uat.investsuite.com/redoc#operation/create_reference_batch_data_custom_reference_batch__post). | `object` | | yes

After uploading data, we get a response back:

=== "Response (Body Content JSON)"
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

To overwrite the data of an instrument, simply provide the reference data fields that need to be overwritten again for that instrument. Only the provided fields will be overwritten.

### QUERY

You can query the uploaded reference data using the query endpoint. A filter can be provided, or the entire cache can be returned when sending an empty payload.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/reference/query/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{
            "instrument_ids": ["BE0003851681"],
            "fields": ["NAME", "ASSET_CLASS"],
        }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/reference/query/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
        "instrument_ids": ["BE0003851681"],
        "fields": ["NAME", "ASSET_CLASS"],
    }
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`instrument_ids` | A list of the IDs of the instruments. | `list` | ["BE0003851681"] | no
`fields` | List of instrument identifiers for which to request data fields. | `list` | ["NAME", "ASSET_CLASS"] | no

The response of such a request is:

=== "Response (Body Content JSON)"

    ```JSON
    {
       "data":{
          "BE0003851681":{
             "name":"Aedifica",
             "asset_class":"EQUITY"
          },
          ...
          },
       "meta":null
    }
    ```
### REMOVE

Filtered data can be removed using the remove endpoint.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/reference/remove/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{
            "instrument_ids": ["BE0003851681"],
            "fields": ["NAME"],
        }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/reference/remove/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
        "instrument_ids": ["BE0003851681"],
        "fields": ["NAME"],
    }
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`instrument_ids` | A list of the IDs of the instruments to be removed. | `list` | ["BE0003851681"] | no
`fields` | List of instrument identifiers to be removed. | `list` | ["NAME"] | no

Only the fields provided for the instruments provided will be removed (it is an AND relation). Either list can be empty, but not both.

After removing data, we get a response back:

=== "Response (Body Content JSON)"
```JSON
{
  "data": {
    "cache_instrument_count": 2,
    "cache_field_count": 3
  }
}
```

This gives information on how much data is left in the cache.

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | Holds information about the current database. | `object` |  | yes
`cache_instrument_count` | The number of instruments that the client has provided custom reference data for. | `integer` | 2 | yes
`cache_field_count` | The number of different fields that the client has provided reference data for, over all instruments. | `integer` | 3 | yes

### CLEAR

All custom reference data can be removed in one go with the clear endpoint.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/reference/clear/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET"
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/reference/clear/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET
    ```

### CSV POST

Reference data consists of instrument-level data of which only the latest value is relevant. Examples of reference data are the ISIN code, asset class, ticker, name, and instrument type. Using this endpoint, a client can upload reference data for specific instruments as **Comma Separated Values (.CSV) file**.

The endpoint accepts a batch of instruments at once, and has a range of predefined fields to upload data.

**Overwriting existing data:** Updating can be done by providing new data values at the instrument/field-level.

=== "Example CSV File"

  ```csv
  instrument_id,country,name,roundlot_size
  BE0003851681,BE,Aedifica,1
  ```

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/reference/csv/" \
    -H "accept: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -H "Content-Type: multipart/form-data" \
    -F "file=@reference-data.csv;type=text/csv"
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/reference/csv/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: multipart/form-data; filename="reference-data.csv"
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`file` | A CSV file that holds reference data with the first line containing header column `instrument_id`, subsequent columns are the field names of reference data. | `list` | cf. example above  | yes
`instrument_id` | The ID of the instrument. This will be used by InvestSuite products to identify the instrument. While the API does not impose any restrictions on the format, but some InvestSuite products do. Make sure to check these restrictions with the products that you will be using before uploading data. **There is not necessarily a relationship to the ISIN.**| `csv column` | cf. example above | yes
`reference_data` | An object holding the actual reference data of the instrument. Available data fields can be seen in the drop-down at [API documentation of this endpoint](https://api.data.uat.investsuite.com/redoc#operation/create_reference_batch_data_custom_reference_batch__post). | `csv column` | cf. example above | yes

After uploading data, we get a response back:

=== "Response (Body Content JSON)"
```JSON
{
  "data": {
    "cache_instrument_count": 1,
    "cache_field_count": 3
  }
}
```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | Holds information about the current database. | `object` |  | yes
`cache_instrument_count` | The number of instruments that the client has provided custom reference data for. | `integer` | 1 | yes
`cache_field_count` | The number of different fields that the client has provided reference data for, over all instruments. | `integer` | 3 | yes

To overwrite the data of an instrument, simply provide the reference data fields that need to be overwritten again for that instrument. Only the provided fields will be overwritten.

## Custom Timeseries

### POST

Timeseries data consist of instrument-level data for which data changes frequently (usually daily) and for which historical values are relevant. Examples of timeseries data are NAV, adjusted price, yield, and interest rate. Using this endpoint, a client can upload timeseries data for specific instruments, and for a specific type.

The endpoint accepts a batch of instruments at once, for the a particular type of timeseries data. Let us look at an example.

**Overwriting existing data:** Updating can be done by providing new data values at the instrument/field-level.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/timeseries/batch/" \                 
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{
            "data": [
                {
                    "instrument_id": "BE0003851681",
                    "values": {
                        "2022-03-07": 158834,
                        "2022-03-08": 156979.4,
                        "2022-03-09": 162473.3
                    },
                    "currency": USD
                },
                {
                    "instrument_id": "BE0003851681",
                    "values": {
                        "2022-03-07": 9615.215,
                        "2022-03-08": 9489.172,
                        "2022-03-09": 9626.016
                    },
                    "currency": USD
                }
            ],
            "timeseries_type": "ADJUSTED_PRICE"
        }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/timeseries/batch/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    Content-Type: application/json
    accept: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
        "data": [
            {
                "instrument_id": "BE0003851681",
                "values": {
                    "2022-03-07": 158834,
                    "2022-03-08": 156979.4,
                    "2022-03-09": 162473.3
                },
                "currency": USD
            },
            {
                "instrument_id": "BE0003851681",
                "values": {
                    "2022-03-07": 9615.215,
                    "2022-03-08": 9489.172,
                    "2022-03-09": 9626.016
                },
                "currency": USD
            }
        ],
        "timeseries_type": "ADJUSTED_PRICE"
    }

    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | A list that holds a timeseries data object for each provided instrument. | `list` |  | yes
`instrument_id` | The ID of the instrument. This will be used by all InvestSuite products to identify the instrument. While the API does not impose any restrictions on the format, some InvestSuite products do. Make sure to check these restrictions with the products that you will be using before uploading data. | `string` | "BE0003851681" | yes
`values` | An object holding the timeseries data for the instrument. The keyword is a datestamp string with format `YYYY-MM-DD` or string Unix timestamp. The value should be a float.  | `object[str, float]` | `{"2022-03-07": 9615.215, "1656337622": 9489.172}` | yes
`currency` | The currency of the values being uploaded | `str` | "USD" | yes (where applicable)
`timeseries_type` | The type of timeseries data that is provided. Available types can be seen in the [API documentation of this endpoint](https://api.data.uat.investsuite.com/redoc#operation/create_timeseries_batch_data_custom_timeseries_batch__post)


After uploading data, we get a response back:

=== "Response (Body Content JSON)"
```JSON
{
  "data": {
    "cache_instrument_count": 2,
    "cache_date_count": 3
  }
}
```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | Holds information about the current database. | `object` |  | yes
`cache_instrument_count` | The number of instruments for which the client has provided custom timeseries data. | `integer` | 2 | yes
`cache_date_count` | The number of different dates for which the client has provided timeseries data, over all instruments. | `integer` | 3 | yes

To overwrite a certain type of timeseries data for one or more instruments on specific dates, simply provide the data for these instruments on the dates to overwrite again.

Also note that trying to upload a timeseries with a different currency than the one already present in the cache will throw an error.

### QUERY

You can query the uploaded timeseries data using the query endpoint. A filter can be provided, or the entire cache for a given timeseries type can be returned. Furthermore, the currencies for each instrument for which the timeseries data has been uploaded previously can be returned as part of the payload as well.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/timeseries/query/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{
            "timeseries_type": "ADJUSTED_PRICE",
            "instrument_ids": ["BE0003851681"],
            "start_date": "2022-03-07",
            "end_date": "2022-03-09",
            "include_currency_meta": True
        }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/timeseries/query/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
        "timeseries_type": "ADJUSTED_PRICE",
        "instrument_ids": ["BE0003851681"],
        "start_date": "2022-03-07",
        "end_date": "2022-03-09",
        "include_currency_meta": True
    }
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`instrument_ids` | A list of the IDs of the instruments. | `list` | ["BE0003851681"] | no
`timeseries_type` | The type of timeseries data. | `str` | "ADJUSTED_PRICE" | yes
`start_date` | The start date of the date range to query, provided as a string of the format `YYYY-MM-DD`. | `str` | "2022-03-07" | no
`end_date` | The end date of the date range to query, provided as a string of the format `YYYY-MM-DD`. | `str` | "2022-03-09" | no
`include_currency_meta` | Whether to include the currencies of the uploaded time series. | `bool` | True | no

The response of such a request is:

=== "Response (Body Content JSON)"

    ```JSON
    {
        "data": {
            "BE0003851681": {
                "2022-03-07": 9615.215,
                "2022-03-08": 9489.172,
                "2022-03-09": 9626.016
            },
            ...
        },
        "meta": {
            "currency_map": {
                "BE0003851681": "USD",
                ...
            }
        }
    }
    ```

### Meta QUERY

You can query the meta data for the custom uploaded timeseries data. Currently this only contains the currency of the values uploaded.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/timeseries/meta/query/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{
            "timeseries_type": "ADJUSTED_PRICE",
            "instrument_ids": ["BE0003851681"],
        }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/timeseries/meta/query/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
        "timeseries_type": "ADJUSTED_PRICE",
        "instrument_ids": ["BE0003851681"],
    }
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`instrument_ids` | A list of the IDs of the instruments. | `list` | ["BE0003851681"] | no
`timeseries_type` | The type of timeseries data. | `str` | "ADJUSTED_PRICE" | yes

The response of such a request is:

=== "Response (Body Content JSON)"

    ```JSON
    {
        "data": {},
        "meta": {
            "currency_map": {
                "BE0003851681": "USD",
                ...
            }
        }
    }
    ```

### REMOVE

Filtered data can be removed using the remove endpoint.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/timeseries/remove/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{
            "instrument_ids": ["BE0003851681"],
            "timeseries_type": "ADJUSTED_PRICE",
            "start_date": "2022-07-07",
            "end_date": "2022-07-08"
        }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/timeseries/remove/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
        "instrument_ids": ["BE0003851681"],
        "timeseries_type": "ADJUSTED_PRICE",
        "start_date": "2022-07-07",
        "end_date": "2022-07-08"
    }
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`instrument_ids` | A list of the IDs of the instruments. | `list` | ["BE0003851681"] | no
`timeseries_type` | The type of timeseries data. | `str` | "ADJUSTED_PRICE" | yes
`start_date` | The start date of the date range to query, provided as a string of the format `YYYY-MM-DD`. | `str` | "2022-03-07" | no
`end_date` | The end date of the date range to query, provided as a string of the format `YYYY-MM-DD`. | `str` | "2022-03-09" | no

Only the date ranges provided for the instruments provided will be removed (it is an AND relation). At least one filter must be provided.

After removing data, we get a response back:

=== "Response (Body Content JSON)"
```JSON
{
  "data": {
    "cache_instrument_count": 2,
    "cache_field_count": 3
  }
}
```

This gives information on how much data is left in the cache.

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | Holds information about the current database. | `object` |  | yes
`cache_instrument_count` | The number of instruments that the client has provided custom reference data for. | `integer` | 2 | yes
`cache_field_count` | The number of different fields that the client has provided reference data for, over all instruments. | `integer` | 3 | yes

### CLEAR

All custom timeseries data for a timeseries type can be removed in one go with the clear endpoint.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/timeseries/clear/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET"
    -d '{
            "timeseries_type": "ADJUSTED_PRICE"
        }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/timeseries/clear/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
        "timeseries_type": "ADJUSTED_PRICE"
    }
    ```

## Custom Composition

### POST
Composition data of an instrument provides a look-through of the underlying instruments according to certain composition types.
Examples are the asset class or country composition of a fund on a certain date.
Using this endpoint, a client can upload composition data on different composition types for specific instruments.
If you require composition data over different points in time (timeseries) use [Custom Composition Timeseries Data](#custom-composition-timeseries-data).

When uploading custom composition data, certain sanity checks are performed by default. These are

- COMPOSITION_TENANT_LEVELS: a check of the uploaded composition levels (e.g., "equity" & "fixed_income" for the ASSET_CLASS composition) against those defined in the tenant classification config. If levels are present in the upload which are not defined in the tenant config, an error is thrown
- COMPOSITION_SUMS_TO_ONE: a check whether the composition weights sum to one (with a small tolerance). If the sum falls outside of 1 +- the tolerance, an error is thrown.

These checks can be bypassed by specifying them in the upload body; however unless necessary for specific reasons we do not recommend to do so.

**Overwriting existing data:** Updating can be done by providing new data values at the composition type/instrument-level (i.e., the entire composition for a given type and instrument gets overwritten).

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/composition/batch/" \                
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{
      "data": [{
    			"instrument_id": "IE00B4L5Y983",
    			"composition_data": {
    				"asset_class": {
    					"equity": 1
    				},
    				"currency": {
    					"gbp": 1
    				},
    				"equity_region": {
    					"emerging": 0.1,
    					"europe_developed": 0.2,
    					"north_america": 0.7
    				}
    			}
    		},
    		{
    			"instrument_id": "IE00BWT41R00",
    			"composition_data": {
    				"equity_sector": {
    					"real_estate": 0.3,
    					"technology": 0.3,
    					"utilities": 0.2
    				},
    				"fixed_income_type": {
    					"government": 1
    				}
    			}
    		}
    	],
    }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/composition/batch/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    Content-Type: application/json
    accept: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
    	"data": [{
    			"instrument_id": "IE00B4L5Y983",
    			"composition_data": {
    				"asset_class": {
    					"equity": 1
    				},
    				"currency": {
    					"gbp": 1
    				},
    				"equity_region": {
    					"emerging": 0.1,
    					"europe_developed": 0.2,
    					"north_america": 0.7
    				}
    			}
    		},
    		{
    			"instrument_id": "IE00BWT41R00",
    			"composition_data": {
    				"equity_sector": {
    					"real_estate": 0.3,
    					"technology": 0.3,
    					"utilities": 0.2
    				},
    				"fixed_income_type": {
    					"government": 1
    				}
    			}
    		}
    	]
    }
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | A list that holds a composition timeseries data object for each provided instrument. | `list` |  | yes
`instrument_id` | The ID of the instrument. This will be used by all InvestSuite products to identify the instrument. While the API does not impose any restrictions on the format, some InvestSuite products do. Make sure to check these restrictions with the products that you will be using before uploading data. | `string` | "BE0003851681" | yes
`composition_data` | An object holding composition data for the provided composition types. Possible composition types are `asset_class`, `credit_rating`, `equity_sector`, `equity_region`, `equity_country`, `equity_industry`, `equity_industry_group`, `fixed_income_type`, `fixed_income_region`, `fixed_income_country`, `fixed_income_region`, `instrument`, `currency`, and `duration`. The API does not impose restrictions on the subtypes, but some InvestSuite products do. Make sure to check these restrictions with the products that you will be using before uploading data. Values for composition should be float. | `object` | | yes
`skip_data_checks` | A list of data checks to be ignored. | `list` |  | no

After uploading data, we get a response back:

=== "Response (Body Content JSON)"
```JSON
{
  "data": {
    "cache_instrument_count": 5,
    "cache_composition_type_count": 8
  },
  "meta": null
}
```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | Holds information about the current database. | `object` |  | yes
`cache_instrument_count` | The number of instruments that the client has provided custom composition data for. | `integer` | 5 | yes
`cache_composition_type_count` | The number of composition types that the client has provided composition data for. | `integer` | 8 | yes

To overwrite a certain type of composition data for one or more instruments, simply provide the data for these instruments and types on the dates to overwrite again.

### QUERY

You can query the uploaded composition data using the query endpoint. A filter can be provided, or the entire cache can be returned by providing an empty payload. Furthermore, the defined composition levels can be returned as part of the meta object in the response body.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/composition/query/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{
            "instrument_ids": ["IE00B4L5Y983"],
            "types": ["asset_class"],
            "include_levels": True
        }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/composition/query/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
        "instrument_ids": ["IE00B4L5Y983"],
        "types": ["asset_class"],
        "include_levels": True
    }
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`instrument_ids` | A list of the IDs of the instruments. | `list` | ["BE0003851681"] | no
`types` | A list of the composition types. | `list` | ["asset_class"] | no
`include_levels` | Whether to include the defined levels. | `bool` | True | no

The response of such a request is:

=== "Response (Body Content JSON)"

    ```JSON
    {
        "data": {
            "IE00B4L5Y983": {
                "ASSET_CLASS": {
                    "equity": 1
                }
            },
            ...
        },
        "meta": {
            "composition_levels": {
                "ASSET_CLASS": ["equity", "fixed_income"],
                ...
            }
        }
    }
    ```

### Meta QUERY

You can query the meta data for the custom uploaded composition data. Currently this only contains the composition levels as defined in the tenant config.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/composition/meta/query/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{
            "types": ["asset_class"]
        }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/composition/meta/query/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
        "types": ["ASSET_CLASS"]
    }
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
types` | A list of the composition types. | `list` | ["asset_class"] | no

The response of such a request is:

=== "Response (Body Content JSON)"

    ```JSON
    {
        "data": {},
        "meta": {
            "composition_levels": {
                "ASSET_CLASS": ["equity", "fixed_income"],
                ...
            }
        }
    }
    ```

### REMOVE

Filtered data can be removed using the remove endpoint.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/composition/remove/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{
            "instrument_ids": ["BE0003851681"],
            "types": ["asset_class"]
        }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/composition/remove/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
        "instrument_ids": ["BE0003851681"],
        "types": ["ASSET_CLASS"]
    }
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`instrument_ids` | A list of the IDs of the instruments. | `list` | ["BE0003851681"] | no
`types` | A list of the composition types. | `list` | ["asset_class"] | no

Only the date ranges provided for the instruments provided will be removed (it is an AND relation). At least one filter must be provided.

After removing data, we get a response back:

=== "Response (Body Content JSON)"
```JSON
{
  "data": {
    "cache_instrument_count": 5,
    "cache_composition_type_count": 8
  },
  "meta": null
}
```

This gives information on how much data is left in the cache.

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | Holds information about the current database. | `object` |  | yes
`cache_instrument_count` | The number of instruments that the client has provided custom reference data for. | `integer` | 2 | yes
`cache_field_count` | The number of different fields that the client has provided reference data for, over all instruments. | `integer` | 3 | yes

### CLEAR

All custom composition data can be removed in one go with the clear endpoint.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/composition/clear/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET"
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/composition/clear/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET
    ```

## Custom Composition Timeseries

### POST

Composition timeseries data of an instrument provides a look-through of the underlying instruments according to certain composition types at certain dates. Examples are the asset class or country composition of a fund on a certain date. Using this endpoint, a client can upload composition data on different composition types for specific instruments, for certain dates (timeseries).

The endpoint accepts a batch of instruments at once. Let us look at an example.

**Overwriting existing data:** Updating can be done by providing new data values at the composition type/instrument/date level (i.e., the entire composition for a given type and instrument on a give day gets overwritten).

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/composition-timeseries/batch/" \                 
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{
            "data": [
                {
                "instrument_id": "IE00B4L5Y983",
                "composition_data": {
                    "ASSET_CLASS": {
                        "2022-01-31": {
                            "equity": 0.5,
                            "money_market": 0.5
                        },
                        "2022-02-28": {
                            "equity": 0.6,
                            "money_market": 0.4
                        }
                    },
                    "EQUITY_REGION": {
                        "2022-01-31": {
                            "europe": 0.3,
                            "north_america": 0.6,
                            "emerging_markets": 0.1
                        },
                        "2022-02-28": {
                            "europe": 0.35,
                            "north_america": 0.65
                        }
                    }
                }
                }
            ]
        }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/composition-timeseries/batch/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    Content-Type: application/json
    accept: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
        "data": [
            {
            "instrument_id": "IE00B4L5Y983",
            "composition_data": {
                "ASSET_CLASS": {
                    "2022-01-31": {
                        "equity": 0.5,
                        "money_market": 0.5
                    },
                    "2022-02-28": {
                        "equity": 0.6,
                        "money_market": 0.4
                    }
                },
                "EQUITY_REGION": {
                    "2022-01-31": {
                        "europe": 0.3,
                        "north_america": 0.6,
                        "emerging_markets": 0.1
                    },
                    "2022-02-28": {
                        "europe": 0.35,
                        "north_america": 0.65
                    }
                }
            }
            }
        ]
    }
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | A list that holds a composition timeseries data object for each provided instrument. | `list` |  | yes
`instrument_id` | The ID of the instrument. This will be used by all InvestSuite products to identify the instrument. While the API does not impose any restrictions on the format, some InvestSuite products do. Make sure to check these restrictions with the products that you will be using before uploading data. | `string` | "BE0003851681" | yes
`composition_data` | An object holding timeseries composition data for the provided composition types. Possible composition types are `ASSET_CLASS`, `CREDIT_RATING`, `EQUITY_SECTOR`, `EQUITY_REGION`, `EQUITY_COUNTRY`, `EQUITY_INDUSTRY`, `EQUITY_INDUSTRY_GROUP`, `FIXED_INCOME_TYPE`, `FIXED_INCOME_REGION`, `FIXED_INCOME_COUNTRY`, `REGION`, `INSTRUMENT`, `CURRENCY`, and `DURATION`. The API does not impose restrictions on the subtypes, but some InvestSuite products do. Make sure to check these restrictions with the products that you will be using before uploading data. A datestamp should be a string with format `YYYY-MM-DD` or string Unix timestamp. | `object[CompositionType, TimestampedCompositionObject]` | | yes

After uploading data, we get a response back:

=== "Response (Body Content JSON)"
```JSON
{
  "data": {
    "cache_instrument_count": 1,
    "cache_composition_type_count": 2,
    "cache_date_count": 6
  }
}
```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | Holds information about the current database. | `object` |  | yes
`cache_instrument_count` | The number of instruments that the client has provided custom composition data for. | `integer` | 1 | yes
`cache_composition_type_count` | The number of composition types that the client has provided composition data for. | `integer` | 2 | yes
`cache_date_count` | The number of different dates that the client has provided timeseries data for, over all instruments. | `integer` | 6 | yes

To overwrite a certain type of composition data for one or more instruments on specific dates, simply provide the data for these instruments and types on the dates to overwrite again.


### QUERY

You can query the uploaded composition timeseries data using the query endpoint. A filter can be provided, or the entire cache can be returned by providing an empty payload. Furthermore, the defined composition levels can be returned as part of the meta object in the response body.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/composition-timeseries/query/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{
            "instrument_ids": ["IE00B4L5Y983"],
            "composition_types": ["ASSET_CLASS"],
            "include_levels": True,
            "start_date": "2022-01-02",
            "end_date": "2022-02-28"
        }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/composition-timeseries/query/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
        "instrument_ids": ["IE00B4L5Y983"],
        "composition_types": ["ASSET_CLASS"],
        "include_levels": True,
        "start_date": "2022-01-02",
        "end_date": "2022-02-28"
    }
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`instrument_ids` | A list of the IDs of the instruments. | `list` | ["BE0003851681"] | no
`composition_types` | A list of the composition types. | `list` | ["ASSET_CLASS"] | no
`start_date` | The start date of the date range to query, provided as a string of the format `YYYY-MM-DD`. | `str` | "2022-03-07" | no
`end_date` | The end date of the date range to query, provided as a string of the format `YYYY-MM-DD`. | `str` | "2022-03-09" | no
`include_levels` | Whether to include the defined levels. | `bool` | True | no

The response of such a request is:

=== "Response (Body Content JSON)"

    ```JSON
    {
        "data": {
            "IE00B4L5Y983": {
                "2022-01-31": {
                    "ASSET_CLASS": {
                        "equity": 0.5,
                        "money_market": 0.5
                    }
                },
                "2022-02-28": {
                    "ASSET_CLASS": {
                        "equity": 0.6,
                        "money_market": 0.4
                    }
                }
            }
            ...
        },
        "meta": {
            "composition_levels": {
                "ASSET_CLASS": ["equity", "fixed_income", "money_market"],
                ...
            }
        }
    }
    ```

### REMOVE

Filtered data can be removed using the remove endpoint.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/composition-timeseries/remove/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{
            "instrument_ids": ["BE0003851681"],
            "composition_types": ["ASSET_CLASS"],
            "start_date": "2022-01-02",
            "end_date": "2022-01-31"
        }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/composition-timeseries/remove/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
        "instrument_ids": ["BE0974293251"],
        "types": ["ASSET_CLASS"],
        "start_date": "2022-01-02",
        "end_date": "2022-01-31"
    }
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`instrument_ids` | A list of the IDs of the instruments. | `list` | ["BE0974293251"] | no
`types` | A list of the composition types. | `list` | ["ASSET_CLASS"] | no
`start_date` | The start date of the date range to remove, provided as a string of the format `YYYY-MM-DD`. | `str` | "2022-03-07" | no
`end_date` | The end date of the date range to remove, provided as a string of the format `YYYY-MM-DD`. | `str` | "2022-03-09" | no

Only the date ranges provided for the instruments and composition types provided will be removed (it is an AND relation). At least one filter must be provided.

After removing data, we get a response back:

=== "Response (Body Content JSON)"
```JSON
{
  "data": {
    "cache_instrument_count": 1,
    "cache_composition_type_count": 2,
    "cache_date_count": 6
  },
  "meta": null
}
```

This gives information on how much data is left in the cache.

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | Holds information about the current database. | `object` |  | yes
`cache_instrument_count` | The number of instruments that the client has provided custom composition data for. | `integer` | 1 | yes
`cache_composition_type_count` | The number of composition types that the client has provided composition data for. | `integer` | 2 | yes
`cache_date_count` | The number of different dates that the client has provided timeseries data for, over all instruments. | `integer` | 6 | yes

### CLEAR

All custom composition timeseries data can be removed in one go with the clear endpoint.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/composition-timeseries/clear/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET"
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/composition-timeseries/clear/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET
    ```


## Custom Attribution

### POST

Attribution data of an instrument provides an overview of how much the underlying instruments attributed to the overall return of the instrument. For example how much each instrument in a fund has attributed to the fund's profit. Using this endpoint, a client can upload attribution data, for certain dates (timeseries).

The endpoint accepts a batch of instruments at once. Let us look at an example.

**Overwriting existing data:** Updating can be done by providing new data values at the day/fund-level (i.e., the entire day/fund gets overwritten with the uploaded data).

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/attribution/batch/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{"data": [
          {
            "fund_id": "FUNDID123456",
            "attribution_data": {
              "2022-03-08": {
                "BE0003851681": -0.005838,
                "BE0974293251": 0.012317
              },
              "2022-03-09": {
                "BE0003851681": 0.017499,
                "BE0974293251": 0.020967
              },
              "2022-03-10": {
                "BE0003851681": -0.013593,
                "BE0974293251": -0.012032
                }
              }
            }
          ]
        }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/attribution/batch/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
        "data": [
            {
                "fund_id": "FUNDID123456",
                "attribution_data": {
                    "2022-03-08": {
                        "BE0003851681": -0.005838,
                        "BE0974293251": 0.012317
                    },
                    "2022-03-09": {
                        "BE0003851681": 0.017499,
                        "BE0974293251": 0.020967
                    },
                    "2022-03-10": {
                        "BE0003851681": -0.013593,
                        "BE0974293251": -0.012032
                    }
                }
            }
        ]
    }

    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | A list that holds an attribution timeseries data object for each provided instrument. | `list` |  | yes
`fund_id` | The ID of the instrument. This will be used by all InvestSuite products to identify the instrument. While the API does not impose any restrictions on the format, some InvestSuite products do. Make sure to check these restrictions with the products that you will be using before uploading data. | `string` | "FUNDID123456" | yes
`attribution_data` | An object holding timeseries attribution data of the instrument. The keyword is a datestamp string with format `YYYY-MM-DD` or string Unix timestamp. The value should be a float. | `object[str, float]` | `"2022-03-09": {"BE0003851681": 0.017499}` | yes

After uploading data, we get a response back:

=== "Response (Body Content JSON)"
```JSON
{
  "data": {
    "cache_fund_count": 1,
    "cache_date_count": 3,
  }
}
```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | Holds information about the current database. | `object` |  | yes
`cache_fund_count` | The number of fund instruments that the client has provided custom attribution data for. | `integer` | 2 | yes
`cache_date_count` | The number of dates that the client has provided attribution data for. | `integer` | 3 | yes

To overwrite a certain type of attribution data for one or more instruments on specific dates, simply provide the data for these instruments on the dates to overwrite again.


### QUERY

You can query the uploaded composition timeseries data using the query endpoint. A filter can be provided, or the entire cache can be returned by providing an empty payload. Furthermore, the defined composition levels can be returned as part of the meta object in the response body.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/composition-timeseries/query/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{
            "fund_ids": ["FUNDID123456"],
            "start_date": ""2022-03-08",
            "end_date": ""2022-03-08"
        }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/attribution/query/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
        "fund_ids": ["FUNDID123456"],
        "start_date": ""2022-03-08",
        "end_date": ""2022-03-08"
    }
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`fund_ids` | A list of the IDs of the funds. | `list` | ["FUNDID123456"] | no
`start_date` | The start date of the date range to query, provided as a string of the format `YYYY-MM-DD`. | `str` | "2022-03-07" | no
`end_date` | The end date of the date range to query, provided as a string of the format `YYYY-MM-DD`. | `str` | "2022-03-09" | no

The response of such a request is:

=== "Response (Body Content JSON)"

    ```JSON
    {
        "data": {
            "FUNDID12345": {
                "2022-03-08": {
                    "BE0003851681": -0.005838,
                    "BE0974293251": 0.012317,
                    ...
                },
                ...
            }
            ...
        },
        "meta": null
    }
    ```

### REMOVE

Filtered data can be removed using the remove endpoint.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/attribution/remove/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{
            "fund_ids": ["FUNDID123456"],
            "start_date": "2022-03-08",
            "end_date": "2022-03-08"
        }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/attribution/remove/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
        "fund_ids": ["FUNDID123456"],
        "start_date": "2022-03-08",
        "end_date": "2022-03-08"
    }
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`fund_ids` | A list of the IDs of the funds. | `list` | ["FUNDID123456"] | no
`start_date` | The start date of the date range to remove, provided as a string of the format `YYYY-MM-DD`. | `str` | "2022-03-07" | no
`end_date` | The end date of the date range to remove, provided as a string of the format `YYYY-MM-DD`. | `str` | "2022-03-09" | no

Only the date ranges provided for the funds provided will be removed (it is an AND relation). At least one filter must be provided.

After removing data, we get a response back:

=== "Response (Body Content JSON)"
```JSON
{
  "data": {
    "cache_fund_count": 1,
    "cache_date_count": 3,
  }
}
```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | Holds information about the current database. | `object` |  | yes
`cache_fund_count` | The number of fund instruments that the client has provided custom attribution data for. | `integer` | 2 | yes
`cache_date_count` | The number of dates that the client has provided attribution data for. | `integer` | 3 | yes

### CLEAR

All custom attribution data can be removed in one go with the clear endpoint.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/attribution/clear/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET"
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/attribution/clear/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET
    ```

## Custom Exchange Rates POST

Exchange rates data consist of historical exchange rates versus a provided base currency, which are used to convert other timeseries such as prices from their currency onto a single currency. Using this endpoint, a client can upload timeseries data for all exchange rates against a provided base currency.

The endpoint accepts a batch of currencies at once. Let us look at an example.

**Overwriting existing data:** Updating can be done by providing new data values at the currency/value-level.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/exchange-rates/batch/" \                 
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{
            "data": [
                {
                    "currency": "EUR",
                    "values": {
                        "2020-01-01": 1.21,
                        "2020-01-02": 1.22,
                        "2020-01-03": 1.21,
                        "2020-01-04": 1.20,
                        "2020-01-05": 1.23,
                    },
                },
                {
                    "currency": "JPY",
                    "values": {
                        "2020-01-01": 102.3,
                        "2020-01-02": 102.2,
                        "2020-01-03": 103.1,
                        "2020-01-04": 104.5,
                        "2020-01-05": 102.9,
                    },
                },
            ],
            "base_currency": "USD",
        },'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/exchange-rates/batch/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    Content-Type: application/json
    accept: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
        "data": [
            {
                "currency": "EUR",
                "values": {
                    "2020-01-01": 1.21,
                    "2020-01-02": 1.22,
                    "2020-01-03": 1.21,
                    "2020-01-04": 1.20,
                    "2020-01-05": 1.23,
                },
            },
            {
                "currency": "JPY",
                "values": {
                    "2020-01-01": 102.3,
                    "2020-01-02": 102.2,
                    "2020-01-03": 103.1,
                    "2020-01-04": 104.5,
                    "2020-01-05": 102.9,
                },
            },
        ],
        "base_currency": "USD",
    }

    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | A list that holds a currency data object for each provided currency. | `list` |  | yes
`base_currency` | The ISO of the base currency. This is the currency against which all exchange rates will be denoted. Once a base currency is chosen, it can not be changed in subsequent uploads, i.e., it is assumed that the base currency is fixed for a tenant. | `string` | "USD" | yes
`currency` | The ISO of the currency. | `string` | "EUR" | yes
`values` | An object holding the exchange rate data for the currency against the base currency. The keyword is a datestamp string with format `YYYY-MM-DD` or string Unix timestamp. The value should be a float.  | `object[str, float]` | `{"2022-03-07": 102, "1656337622": 102}` | yes

After uploading data, we get a response back:

=== "Response (Body Content JSON)"
```JSON
{
  "data": {
    "cache_instrument_count": 2,
    "cache_date_count": 3
  }
}
```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | Holds information about the current database. | `object` |  | yes
`cache_instrument_count` | The number of currencies for which the client has provided custom exchane rate data. | `integer` | 2 | yes
`cache_date_count` | The number of different dates for which the client has provided exchange rate data, over all currencies. | `integer` | 3 | yes

To overwrite data for one or more currencies on specific dates, simply provide the data for these currencies on the dates to overwrite again.

Also note that trying to upload data with a different base currency than the one alrady present in the cache will throw an error.

## Custom Exchange Rate QUERY

You can query the uploaded exchange rate data using the query endpoint. A filter can be provided, or the entire cache can be returned. Furthermore, the base currency for which the exchange rate data has been uploaded previously can be returned as part of the payload as well.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/exchange-rates/query/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{
            "currencies": ["EUR", "JPY"],
            "start_date": "2020-01-01",
            "end_date": "2020-01-05",
            "include_base_currency_meta": True
        }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/exchange-rates/query/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
        "currencies": ["EUR", "JPY"],
        "start_date": "2020-01-01",
        "end_date": "2020-01-05",
        "include_base_currency_meta": True
    }
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`currencies` | A list of the IDs of the currencies. | `list` | ["EUR"] | no
`start_date` | The start date of the date range to query, provided as a string of the format `YYYY-MM-DD`. | `str` | "2022-03-07" | no
`end_date` | The end date of the date range to query, provided as a string of the format `YYYY-MM-DD`. | `str` | "2022-03-09" | no
`include_base_currency_meta` | Whether to include the base currency of the uploaded exchange rates. | `bool` | True | no

The response of such a request is:

=== "Response (Body Content JSON)"

    ```JSON
    {
        "data": {
            "EUR": {
                "2022-03-07": 1.21,
                "2022-03-08": 1.22,
                "2022-03-09": 1.21
            },
            ...
        },
        "meta": {
            "base_currency": "USD"
        }
    }
    ```

## Custom Exchange Rate Meta QUERY

You can query the meta data for the custom uploaded exchange rates data. Currently this only contains the base currency of the values uploaded.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/exchange-rates/meta/query/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{}'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/exchange-rates/meta/query/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
    }
    ```

The response of such a request is:

=== "Response (Body Content JSON)"

    ```JSON
    {
        "data": {},
        "meta": {
            "base_currency": "USD"
        }
    }
    ```

## Custom Exchange Rates REMOVE

Filtered data can be removed using the remove endpoint.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/exchange-rates/remove/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{
            "start_date": "2020-01-03",
            "end_date": "2020-01-05",
            "currencies": ["EUR"]
        }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/exchange-rates/remove/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
        "start_date": "2020-01-03",
        "end_date": "2020-01-05",
        "currencies": ["EUR"]
    }
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`currencies` | A list of the IDs of the instruments. | `list` | ["EUR"] | no
`start_date` | The start date of the date range to remove, provided as a string of the format `YYYY-MM-DD`. | `str` | "2022-03-07" | no
`end_date` | The end date of the date range to remove, provided as a string of the format `YYYY-MM-DD`. | `str` | "2022-03-09" | no

Only the date ranges provided for the currencies provided will be removed (it is an AND relation). At least one filter must be provided.

After removing data, we get a response back:

=== "Response (Body Content JSON)"
```JSON
{
  "data": {
    "cache_instrument_count": 2,
    "cache_field_count": 3
  }
}
```

This gives information on how much data is left in the cache.

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | Holds information about the current database. | `object` |  | yes
`cache_instrument_count` | The number of currencies that the client has provided custom reference data for. | `integer` | 2 | yes
`cache_field_count` | The number of different fields that the client has provided reference data for, over all instruments. | `integer` | 3 | yes

## Custom Exchange Rates CLEAR

All custom exchange rates data can be removed in one go with the clear endpoint.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.uat.investsuite.com/data/custom/exchange-rates/clear/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET"
    -d '{
        }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/exchange-rates/clear/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {
    }
    ```
