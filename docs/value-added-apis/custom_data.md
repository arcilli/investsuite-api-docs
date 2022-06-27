---
title: Custom Data API
---

## Service Description

InvestSuite offers a range of products, all using financial data. Each product has access a few to market data providers. Sometimes the necessary data is not available with these providers, or a client may want to upload their own data to guarantee exactly the same numbers in the InvestSuite products as they report themselves. In that case, the client will upload the custom data to InvestSuite via the API endpoints as described on this page.  This then takes precedence on the data from the provider.

The Financial Data API accepts custom data via several endpoints, each accepting a specific type of data:

- [Custom Reference Data](https://api.data.uat.investsuite.com/redoc#operation/create_reference_batch_data_custom_reference_batch__post)
- [Custom Reference Data CSV](https://api.data.uat.investsuite.com/redoc#operation/create_reference_batch_data_custom_reference_batch__post)
- [Custom Timeseries Data](https://api.data.uat.investsuite.com/redoc#operation/create_timeseries_batch_data_custom_timeseries_batch__post)
- [Custom Composition Data](https://api.data.uat.investsuite.com/redoc#operation/create_composition_batch_data_custom_composition_batch__post)
- [Custom Composition Timeseries Data](https://api.data.uat.investsuite.com/redoc#operation/create_composition_timeseries_batch_data_custom_composition_timeseries_batch__post)
- [Custom Attribution Data](https://api.data.uat.investsuite.com/redoc#operation/create_attribution_batch_data_custom_attribution_batch__post)

The client will most likely use a combination of these endpoints. Below, we elaborate further on how to use these endpoints in practice.


## Custom Reference Data

Reference data consists of instrument-level data of which only the latest value is relevant. Examples of reference data are the ISIN code, asset class, ticker, name, and instrument type. Using this endpoint, a client can upload reference data for specific instruments.

The endpoint accepts a batch of instruments at once, and has a range of predefined fields to upload data.


=== "HTTP Request"

    ```HTTP
    POST /data/custom/reference/batch/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    Content-Type: application/json
    X-TENANT-ID: {your_identifier}
    X-Api-Key: {YOUR_API_SECRET_KEY}

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

=== "Curl Request"

    ```bash
    curl -X "POST" \                 
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: {your_identifier}" \
    -H "X-Api-Key: {YOUR_API_SECRET_KEY}" \   
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
    https://api.data.uat.investsuite.com/data/custom/reference/batch/
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | A list that holds a reference data object for each provided instrument. | `list` |  | yes
`instrument_id` | The ID of the instrument. This will be used by InvestSuite products to identify the instrument. While the API does not impose any restrictions on the format, but some InvestSuite products do. Make sure to check these restrictions with the products that you will be using before uploading data. **There is not necessarily a relationship to the ISIN.**| `string` | "US2546871060" | yes
`reference_data` | An object holding the actual reference data of the instrument. Available data fields can be seen in the drop-down at [API documentation of this endpoint](https://api.data.uat.investsuite.com/redoc#operation/create_reference_batch_data_custom_reference_batch__post). | `object` | | yes

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

To overwrite the data of an instrument, simply provide the reference data fields that need to be overwritten again for that instrument. Only the provided fields will be overwritten.

## Custom Reference CSV Data

Reference data consists of instrument-level data of which only the latest value is relevant. Examples of reference data are the ISIN code, asset class, ticker, name, and instrument type. Using this endpoint, a client can upload reference data for specific instruments as **Comma Separated Values (.CSV) file**.

The endpoint accepts a batch of instruments at once, and has a range of predefined fields to upload data.


=== "Example CSV File"

  ```csv
  instrument_id,country,name,roundlot_size
  US0378331005,US,APPLE,1
  ```

=== "HTTP Request"

    ```HTTP
    POST /data/custom/reference/batch/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: multipart/form-data; filename="reference-data.csv"
    X-TENANT-ID: {your_identifier}
    X-Api-Key: {YOUR_API_SECRET_KEY}
    ```

=== "Curl Request"

    ```bash
    curl -X 'POST' \
    'https://api.data.uat.investsuite.com/data/custom/reference/csv/' \
    -H 'accept: application/json' \
    -H 'X-TENANT-ID: {your_identifier}' \
    -H 'X-Api-Key: {YOUR_API_SECRET_KEY}' \
    -H 'Content-Type: multipart/form-data' \
    -F 'file=@reference-data.csv;type=text/csv'
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`file` | A CSV file that holds reference data with the first line containing header column `instrument_id`, subsequent columns are the field names of reference data. | `list` | cf. example above  | yes
`instrument_id` | The ID of the instrument. This will be used by InvestSuite products to identify the instrument. While the API does not impose any restrictions on the format, but some InvestSuite products do. Make sure to check these restrictions with the products that you will be using before uploading data. **There is not necessarily a relationship to the ISIN.**| `csv column` | cf. example above | yes
`reference_data` | An object holding the actual reference data of the instrument. Available data fields can be seen in the drop-down at [API documentation of this endpoint](https://api.data.uat.investsuite.com/redoc#operation/create_reference_batch_data_custom_reference_batch__post). | `csv column` | cf. example above | yes

After uploading data, we get a response back:

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

## Custom Timeseries Data

Timeseries data consist of instrument-level data for which data changes frequently (usually daily) and for which historical values are relevant. Examples of timeseries data are NAV, adjusted price, yield, and interest rate. Using this endpoint, a client can upload timeseries data for specific instruments, and for a specific type.

The endpoint accepts a batch of instruments at once, for the a particular type of timeseries data. Let us look at an example.

=== "HTTP Request"

    ```HTTP
    POST /data/custom/timeseries/batch/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    Content-Type: application/json
    accept: application/json
    X-TENANT-ID: {your_identifier}
    X-Api-Key: {YOUR_API_SECRET_KEY}

    {
        "data": [
            {
                "instrument_id": "US0378331005",
                "values": {
                    "2022-03-07": 158834,
                    "2022-03-08": 156979.4,
                    "2022-03-09": 162473.3
                }
            },
            {
                "instrument_id": "US2546871060",
                "values": {
                    "2022-03-07": 9615.215,
                    "2022-03-08": 9489.172,
                    "2022-03-09": 9626.016
                }
            }
        ],
        "timeseries_type": "ADJUSTED_PRICE"
    }

    ```

=== "Curl Request"

    ```bash
    curl -X "POST" \                 
    -H "Content-Type: application/json" \
    -H "accept: application/json" \
    -H "X-TENANT-ID: {your_identifier}" \
    -H "X-Api-Key: {YOUR_API_SECRET_KEY}" \   
    -d '{
            "data": [
                {
                    "instrument_id": "US0378331005",
                    "values": {
                        "2022-03-07": 158834,
                        "2022-03-08": 156979.4,
                        "2022-03-09": 162473.3
                    }
                },
                {
                    "instrument_id": "US2546871060",
                    "values": {
                        "2022-03-07": 9615.215,
                        "2022-03-08": 9489.172,
                        "2022-03-09": 9626.016
                    }
                }
            ],
            "timeseries_type": "ADJUSTED_PRICE"
        }'
    https://api.data.uat.investsuite.com/data/custom/timeseries/batch/
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | A list that holds a timeseries data object for each provided instrument. | `list` |  | yes
`instrument_id` | The ID of the instrument. This will be used by all InvestSuite products to identify the instrument. While the API does not impose any restrictions on the format, some InvestSuite products do. Make sure to check these restrictions with the products that you will be using before uploading data. | `string` | "US2546871060" | yes
`values` | An object holding the timeseries data for the instrument. The keyword is a datestamp string with format `YYYY-MM-DD` or string Unix timestamp. The value should be a float.  | `object[str, float]` | `{"2022-03-07": 9615.215, "1656337622": 9489.172}` | yes
`timeseries_type` | The type of timeseries data that is provided. Available types can be seen in the [API documentation of this endpoint](https://api.data.uat.investsuite.com/redoc#operation/create_timeseries_batch_data_custom_timeseries_batch__post)


After uploading data, we get a response back:

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

## Custom Composition Data
Composition data of an instrument provides a look-through of the underlying instruments according to certain composition types.
Examples are the asset class or country composition of a fund on a certain date.
Using this endpoint, a client can upload composition data on different composition types for specific instruments.
If you require composition data over different points in time (timeseries) use [Custom Composition Timeseries Data](#custom-composition-timeseries-data).

=== "HTTP Request"

    ```HTTP
    POST /data/custom/composition/batch/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    Content-Type: application/json
    accept: application/json
    X-TENANT-ID: {your_identifier}
    X-Api-Key: {YOUR_API_SECRET_KEY}

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

=== "Curl Request"

    ```bash
    curl -X "POST" \                 
    -H "Content-Type: application/json" \
    -H "accept: application/json" \
    -H "X-TENANT-ID: {your_identifier}" \
    -H "X-Api-Key: {YOUR_API_SECRET_KEY}" \
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
    	]
    }'
    https://api.data.uat.investsuite.com/data/custom/composition/batch/
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | A list that holds a composition timeseries data object for each provided instrument. | `list` |  | yes
`instrument_id` | The ID of the instrument. This will be used by all InvestSuite products to identify the instrument. While the API does not impose any restrictions on the format, some InvestSuite products do. Make sure to check these restrictions with the products that you will be using before uploading data. | `string` | "US2546871060" | yes
`composition_data` | An object holding composition data for the provided composition types. Possible composition types are `ASSET_CLASS`, `CREDIT_RATING`, `EQUITY_SECTOR`, `EQUITY_REGION`, `EQUITY_COUNTRY`, `EQUITY_INDUSTRY`, `EQUITY_INDUSTRY_GROUP`, `FIXED_INCOME_TYPE`, `FIXED_INCOME_REGION`, `FIXED_INCOME_COUNTRY`, `REGION`, `INSTRUMENT`, `CURRENCY`, and `DURATION`. The API does not impose restrictions on the subtypes, but some InvestSuite products do. Make sure to check these restrictions with the products that you will be using before uploading data. Values for composition should be float. | `object` | | yes

After uploading data, we get a response back:

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

## Custom Composition Timeseries Data

Composition timeseries data of an instrument provides a look-through of the underlying instruments according to certain composition types at certain dates. Examples are the asset class or country composition of a fund on a certain date. Using this endpoint, a client can upload composition data on different composition types for specific instruments, for certain dates (timeseries).

The endpoint accepts a batch of instruments at once. Let us look at an example.

=== "HTTP Request"

    ```HTTP
    POST /data/custom/composition-timeseries/batch/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    Content-Type: application/json
    accept: application/json
    X-TENANT-ID: {your_identifier}
    X-Api-Key: {YOUR_API_SECRET_KEY}

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

=== "Curl Request"

    ```bash
    curl -X "POST" \                 
    -H "Content-Type: application/json" \
    -H "accept: application/json" \
    -H "X-TENANT-ID: {your_identifier}" \
    -H "X-Api-Key: {YOUR_API_SECRET_KEY}" \
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
    https://api.data.uat.investsuite.com/data/custom/composition-timeseries/batch/
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | A list that holds a composition timeseries data object for each provided instrument. | `list` |  | yes
`instrument_id` | The ID of the instrument. This will be used by all InvestSuite products to identify the instrument. While the API does not impose any restrictions on the format, some InvestSuite products do. Make sure to check these restrictions with the products that you will be using before uploading data. | `string` | "US2546871060" | yes
`composition_data` | An object holding timeseries composition data for the provided composition types. Possible composition types are `ASSET_CLASS`, `CREDIT_RATING`, `EQUITY_SECTOR`, `EQUITY_REGION`, `EQUITY_COUNTRY`, `EQUITY_INDUSTRY`, `EQUITY_INDUSTRY_GROUP`, `FIXED_INCOME_TYPE`, `FIXED_INCOME_REGION`, `FIXED_INCOME_COUNTRY`, `REGION`, `INSTRUMENT`, `CURRENCY`, and `DURATION`. The API does not impose restrictions on the subtypes, but some InvestSuite products do. Make sure to check these restrictions with the products that you will be using before uploading data. A datestamp should be a string with format `YYYY-MM-DD` or string Unix timestamp. | `object[CompositionType, TimestampedCompositionObject]` | | yes

After uploading data, we get a response back:

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

## Custom Attribution Data

Attribution data of an instrument provides an overview of how much the underlying instruments attributed to the overall return of the instrument. For example how much each instrument in a fund has attributed to the fund's profit. Using this endpoint, a client can upload attribution data, for certain dates (timeseries).

The endpoint accepts a batch of instruments at once. Let us look at an example.

=== "HTTP Request"

    ```HTTP
    POST /data/custom/attribution/batch/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    Content-Type: application/json
    accept: application/json
    X-TENANT-ID: {your_identifier}
    X-Api-Key: {YOUR_API_SECRET_KEY}

    {
        "data": [
            {
                "fund_id": "US78462F1030",
                "attribution_data": {
                    "2022-03-08": {
                        "US0378331005": -0.005838,
                        "US88160R1014": 0.012317
                    },
                    "2022-03-09": {
                        "US0378331005": 0.017499,
                        "US88160R1014": 0.020967
                    },
                    "2022-03-10": {
                        "US0378331005": -0.013593,
                        "US88160R1014": -0.012032
                    }
                }
            }
        ]
    }

    ```

=== "Curl Request"

    ```bash
    curl -X "POST" \                 
    -H "Content-Type: application/json" \
    -H "accept: application/json" \
    -H "X-TENANT-ID: {your_identifier}" \
    -H "X-Api-Key: {YOUR_API_SECRET_KEY}" \
    -d '{
            "data": [
                {
                    "fund_id": "US78462F1030",
                    "attribution_data": {
                        "2022-03-08": {
                            "US0378331005": -0.005838,
                            "US88160R1014": 0.012317
                        },
                        "2022-03-09": {
                            "US0378331005": 0.017499,
                            "US88160R1014": 0.020967
                        },
                        "2022-03-10": {
                            "US0378331005": -0.013593,
                            "US88160R1014": -0.012032
                        }
                    }
                }
            ]
        }'
    https://api.data.uat.investsuite.com/data/custom/attribution/batch/
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | A list that holds an attribution timeseries data object for each provided instrument. | `list` |  | yes
`fund_id` | The ID of the instrument. This will be used by all InvestSuite products to identify the instrument. While the API does not impose any restrictions on the format, some InvestSuite products do. Make sure to check these restrictions with the products that you will be using before uploading data. | `string` | "US78462F1030" | yes
`attribution_data` | An object holding timeseries attribution data of the instrument. The keyword is a datestamp string with format `YYYY-MM-DD` or string Unix timestamp. The value should be a float. | `object[str, float]` | `"2022-03-09": {"US0378331005": 0.017499}` | yes

After uploading data, we get a response back:

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
