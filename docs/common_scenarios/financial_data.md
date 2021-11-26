---
title: Financial data service
---

## Context

The Financial Data API allows you to integrate with the InvestSuite Financial Data services. These services evolve around investment products and securities such as stocks, ETFs, funds, cryptocurrencies ... At the time of writing, the Financial Data API scope is limited. With the current API endpoints you can query which instruments are attributed to the universe used in your Robo Advisor and Self Investor installations. 

During the first half of 2022 we intend to extend these services in two ways. Firstly, so that you can add data to instruments in your universe. This is applicable in the siutation where InvestSuite does not get the all or some of the data from the market data provider. This applies for instance to data on OTC funds but is also applicable when you retrieve data on securities from market data providers to which InvestSuite is not connected. Data that you pass will obviously only be present in your installation.

Secondly, through these services for certain instruments InvestSuite intends to expose _data enrichments_. Examples of such enrichments are additional metrics to support investors in composing their portfolios and company logos for listed stocks.

## Instrument universe

Financial instruments in general are assets that hold capital and can be traded in the market. Examples are shares, bonds, funds. To use StoryTeller, Optimizer, Self Investor, Robo Advisor you define an _instrument universe_. This is the collection of instruments that can be used to trade or report on. When you query `GET /financial-data/instruments/` the return will hold all instruments in the assigned universe.

Getting the instrument universe is as simple as querying the whole Instruments collection.

=== "HTTP"

    ```HTTP 
    GET /financial-data/instruments/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Authorization: Bearer {access_token_string}
    ```

=== "curl"

    ```bash
    curl -X GET 'https://api.sandbox.investsuite.com/financial-data/instruments/' \
    --H 'Authorization: Bearer {access_token_string}'
    ```

## Getting instrument details

To retrieve a single instrument request GET /financial-data/instruments/{instrument_id}/. This will return basic data on an instrument: its name, reference ID, type, end-of-day price and lookthrough data.

=== "HTTP"

    ```HTTP 
    GET /financial-data/instruments/BE0974293251/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Authorization: Bearer {access_token_string}
    ```

=== "curl"

    ```bash
    curl -X GET 'https://api.sandbox.investsuite.com/financial-data/instruments/BE0974293251/' \
    --H 'Authorization: Bearer {access_token_string}'
    ```

Response:
```JSON
{
    "id": "BE0974293251",
    "external_id": "BE0974293251",
    "name": "ANHEUSER-BUSCH INBEV",
    "type": "STOCK",
    "price": 54.35,
    "look_through": {
        "asset_classes": {
            "alternatives": 0.0,
            "bonds": 0.0,
            "commodities": 0.0,
            "stocks": 1.0,
            "cash": 0.0
        },
        "regions": {
            "bonds": {
                "asia_pacific_developed": 0.0,
                "emerging": 0.0,
                "europe_developed": 0.0,
                "north_america": 0.0
            },
            "stocks": {
                "asia_pacific_developed": 0.0,
                "emerging": 0.0,
                "europe_developed": 1.0,
                "north_america": 0.0
            }
        },
        "bond_types": null,
        "sectors": {
            "basic_materials": 0.0,
            "consumer_cyclical": 0.0,
            "consumer_defensive": 1.0,
            "communication_services": 0.0,
            "energy": 0.0,
            "financial_services": 0.0,
            "healthcare": 0.0,
            "industrials": 0.0,
            "real_estate": 0.0,
            "technology": 0.0,
            "utilities": 0.0
        }
    }
}
```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`id` | The instrument's external identifier. For instance: ISIN. | `string` | BE0974293251 | yes
`external_id` | A unique external identifier for this entity, also referred to as Reference ID. This identifier can be any string used in the client's system to identify this entity. This field is not checked for uniqueness. | `string <= 64 characters` | BE0974293251 | yes
`name` | The instrument's name | `string <= 128 characters` | ANHEUSER-BUSCH INBEV | no
`type` | The instrument's type. For instance: stock, etf. | `Enum("STOCK", "BOND", "ETF", "MUTUAL_FUND", "TOKEN", "INDEX", "CURRENCY", "INTEREST_RATE")` | STOCK | no
`price` | The instrument's last day closing price. | decimal | 54.35 | no
`look_through` | The instrument's regional, sectoral and asset type spread. | object |  | no




