---
title: Themes API
---

## Service Description

The InvestSuite quant team composes and actively manages a list of Themes or thematic instrument groups such as Healthcare, Clean & Green Economy, Social Trends each holding an applicable list of financial instruments. The aim of the Themes Value-Added-API service is to offer a means to filter the instrument universe to their personal preference and view. To retrieve Themes and selected instruments within them perform a `GET` request against the  `/themes/` endpoint:

- [Themes API Redoc Documentation](https://api.data.investsuite.com/redoc#tag/Themes)
- [Themes API Swagger UI Interface](https://api.data.investsuite.com/docs#/Themes/)

Themes can be grouped into Metathemes. A Metatheme consists of multiple themes and will combine their instruments with its own.

## Themes GET

Request Themes and Metathemes instruments and metadata by passing your tenant identifier.

=== "Curl Request"

    ```bash
    curl -X "GET" \
    "https://api.data.investsuite.com/themes/?include_children=false" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET"
    ```

=== "HTTP Request"

    ```HTTP
    GET /themes/?include_children=false HTTP/1.1
    Host: api.data.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET
    ```

Query Parameter Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`include_children` | By default Metathemes that consist of multiple child Themes will not include child instruments. Setting `include_children` to `true` will include the instruments of child Themes into the Metathemes data. | boolean: `true`\|`false` |  | No, default `false`


After the request, we get the following example response:

=== "Response (Body Content JSON)"
    ```JSON
    [
        {
            "name": "theme1",
            "description": "Description of theme1.",
            "type": "theme",
            "children": [],
            "instruments": [
                {
                    "isin": "ISIN1234",
                    "name": "CompanyXYZ Inc."
                },
                ...
            ]
            "date_modified": "2022-04-06T14:34:32.425959"
        },
        ...
        {
            "name": "metatheme1",
            "description": "Description of metatheme2.",
            "type": "metatheme",
            "children": ["theme1"],
            "instruments": [
                {
                    "isin": "ISIN5678",
                    "name": "Enterprise Corp."
                },
                ...
            ]
            "date_modified": "2022-04-06T14:34:32.425959"
        }
    ]
    ```

## InvestSuite Theme Screening Methodology

The screening process consists of two-phases each of which is based on a automated process using human-in-the-loop artificial intelligence.

1. Theme identification: Themes and Thematic Investments are manually identified based on social trends or based on popular investment products (Thematic ETFs). A description and criteria for screening are drafted.

2. Instrument screening: We automatically check the main activities of the instrument companies and their descriptions. The automated screening is then followed by extensive manual screening by humans and serves as a guide. Currently, the majority of the screening work is done by humans with automated suggestions.
