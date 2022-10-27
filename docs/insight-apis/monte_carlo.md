---
title: Monte Carlo API
---

## Service Description

This API endpoint can be used to simulate a range of possible future returns for a portfolio, based on confidence intervals. This can be based on the historical returns of the asset classed the portfolio is exposed to, or a set of expected returns over the short and long term. When using expected returns, the short and long term estimates for the returns and volatilities of the asset classes (which InvestSuite obtains from a variety of sources) are used to calculate the simulations. A linear smoothing is applied to change the distributions over time. InvestSuite updates these expectations in a timely fashion.

The Financial Data API endpoint for the Monte Carlo simulations is accessed through the following endpoint:

- [Future Performance](https://api.data.investsuite.com/redoc#operation/future_performance_future__post)

Below, we elaborate further on how to use this endpoints in practice.

## Future Data POST

The endpoint has a range of predefined fields to query data for. Let us look at an example.

=== "Curl Request"

    ```bash
    curl -X "POST" \
    "https://api.data.investsuite.com/performance/future/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    -d '{
            "asset_classes": {
            "EQUITY": [0.8, 1],
            "FIXED_INCOME": [0, 0.2]
            },
            "start_date": "2022-01-31",
            "end_date": "2022-12-31",
            "currency": "EUR",
            "start_amount": 1000,
            "recurring_deposit_amount": 50,
            "recurring_deposit_frequency": "M",
            "sample_frequency": "M",
            "quantiles": [0.05, 0.5, 0.95],
            "use_expected_returns": false
        }'
    ```

=== "HTTP Request"

    ```HTTP
    POST /performance/future/ HTTP/1.1
    Host: api.data.investsuite.com
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET
    accept: application/json
    Content-Type: application/json

    {
        "asset_classes": {
            "EQUITY": [0.8, 1],
            "FIXED_INCOME": [0, 0.2]
        },
        "start_date": "2022-01-31",
        "end_date": "2022-12-31",
        "currency": "EUR",
        "start_amount": 1000,
        "recurring_deposit_amount": 50,
        "recurring_deposit_frequency": "M",
        "sample_frequency": "M",
        "quantiles": [0.05, 0.5, 0.95],
        "use_expected_returns": false
    }

    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`asset_classes` | A dictionary mapping asset classes to portfolio upper and lower weights. | `dict` | {"EQUITY": [0.8, 1], "FIXED_INCOME": [0, 0.2]} | yes
`start_date` | Start date of the scenarios. | `date` | "2022-01-31" | yes
`end_date` | End date of the scenarios. | `date` | "2022-12-31" | yes
`currency` | The currency of the portfolio in ISO format. | `str` | "USD" | no
`start_amount` | Start amount of the portfolio (in base currency) - i.e. amount put in at start_date. | `float` | 1000 | no
`recurring_deposit_amount` | Amount assumed to be added/subtracted at the provided frequency. | `float` | 100 | no
`recurring_deposit_frequency` | Frequency of the deposit amount. Options are "D", "W", "M" or "Y". | `str` | "M" | no
`sample_frequency` | Frequency for which the scenarios should be generated. Options are "D", "W", "M" or "Y". | `str` | "M" | no
`quantiles` | The quantiles to calculate and return (expressed in decimal form). | `list` | [0.05, 0.5, 0.95] | no
`use_expected_returns` | Whether the simulations need to are based on projected returns, not historical ones. | `bool` | false | no
`asset_class_mapping` | Instruments to use for the historical asset class returns. | `dict` | {"EQUITY": "BE0974293251", "FIXED_INCOME": "IE00B4L5Y983"} | no
`asset_class_st_mean_and_stdev` | Mean and standard deviation for the short term projected asset class returns. | `dict` | {"EQUITY": {"mean": 0.25, "stdev": 0.35}, "FIXED_INCOME": {"mean": 0.005, "stdev": 0.04}} | no
`asset_class_lt_mean_and_stdev` | Mean and standard deviation for the long term projected asset class returns. | `dict` | {"EQUITY": {"mean": 0.15, "stdev": 0.25}, "FIXED_INCOME": {"mean": 0.005, "stdev": 0.04}} | no
`short_to_long_term_transition_cut_off` | Cut-off between the short and long term distributions, in years. | `int` | 5 | no

The long term and short term distribution parameters can be provided by mapping asset classes to the following structure.

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`mean` | The mean of the distribution. | `float` | 0.15 | yes
`stdev` | The standard deviation of the distribution. | `float` | 0.25 | yes


After uploading data, we get a response back:

=== "Response (Body Content JSON)"
```JSON
{
    "data": {
        "value": {
            "0.05": {
                "2022-01-31": 1050.0,
                "2022-02-28": 1035.8986317433569,
                ...
                "2022-11-30": 1402.444917327639,
                "2022-12-31": 1450.056624826056
            },
            "0.5": {
                "2022-01-31": 1050.0,
                "2022-02-28": 1109.6624596515082,
                ...
                "2022-11-30": 1682.6038711807148,
                "2022-12-31": 1749.7551854182339
            },
            "0.95": {
                "2022-01-31": 1050.0,
                "2022-02-28": 1188.9452142854648,
                ...
                "2022-11-30": 2032.411301502351,
                "2022-12-31": 2127.5945389625517
            }
        },
        "deposits": {
            "deposits": {
                "2022-01-31": 50.0,
                "2022-02-28": 100.0,
                ...
                "2022-11-30": 550.0,
                "2022-12-31": 600.0
            }
        },
        "return": {
            "0.05": {
                "2022-01-31": 1000.0,
                "2022-02-28": 935.8986317433569,
                ...
                "2022-11-30": 852.4449173276389,
                "2022-12-31": 850.0566248260559
            },
            "0.5": {
                "2022-01-31": 1000.0,
                "2022-02-28": 1009.6624596515082,
                ...
                "2022-11-30": 1132.6038711807148,
                "2022-12-31": 1149.7551854182339
            },
            "0.95": {
                "2022-01-31": 1000.0,
                "2022-02-28": 1088.9452142854648,
                ...
                "2022-11-30": 1482.411301502351,
                "2022-12-31": 1527.5945389625517
            }
        }
    },
    "meta": null
}
```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | Holds all the Monte Carlo simulation data. | `object` |  | yes
`value` | The simulated future value of the portfolio of the requested quantiles. | `dict` |  | yes
`deposits` | The cumulative deposits over time. | `dict` |  | yes
`return` | The simulated future value of the portfolio of the requested quantiles, minus the deposits. | `dict` |  | yes
