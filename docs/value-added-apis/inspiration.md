---
title: Inspiration Value Added API
---

## Service Description

The Inspiration API provides groupings of instruments by automated curation without the manual work of your analyst/content team. For instance, it maintains stocks with top-scores on valuation, growth, and financial health, or analyst favourites and dividend stars. The Inspiration instrument groupings rely on InvestSuite's XRay analyses implemented by our Quant Team.

To retrieve Inspiration and up-to-date instruments within them perform a `GET` request against the  `/inspiration/` endpoint.
A GET request against the `/inspiration/definition/` endpoint will return the metadata (identifier, description, name, query definition) available to the tenant.

- [Inspiration API Redoc Documentation](https://api.data.dev.investsuite.com/redoc#tag/Inspiration)
- [Inspiration API Swagger UI Interface](https://api.data.dev.investsuite.com/docs#/Inspiration/)

** ⚠️ NOTE: V1 version does not yet support user-defined Inspiration Definitions.**

## Usage & Model

### Inspiration
GET Inspirations for a tenant with updated instruments matching the provided Inspiration Definition at endpoint `/inspiration/`.

One or multiple `id` parameter queries can be passed with optional to filter on Inspirations of interest.
`id` is optional and by default gets all available Inspirations.
To obtain available identifiers in the Inspiration Definitions via a GET request the `/inspiration/definition/` endpoint (cf. below).

=== "Request"

    ```HTTP hl_lines="1"
    GET /inspiration/
        ?[id={inspiration_identifier_1}] HTTP/1.1
    Host: api.data.uat.investsuite.com
    Authorization: Bearer {access_token_string}
    ```

=== "Response (body)"

    ```JSON hl_lines="10"
    [{
        "name": "str: The formatted full name of your inspiration, e.g. Top Financial Health",
        "id": "str: The short identifier name, use underscore seperor, e.g. top_financial_health",
        "description": "str: Description of the inspiration, e.g. Financially healthy companies.",
        "type": "str: InvestSuite Thematic-datatype field (inspiration|theme|metatheme)"".
        "custom": "bool: Custom to tenant or whitelabel (false|true)"",
        "query": {
            "xray": "$FINANCIAL_HEALTH_STARS >= 0"
        },
        "instruments": [
            {
                "isin": "ISIN identifier of instrument, e.g. ISIN1234.",
                "name": "Full extended company name, e.g., XYZ Corporation."
            }
            ...
        ]
    },
    ...
    ]
    ```

### Inspiration Definitions
GET the Inspiration Definition metadata without the instruments at the `/inspiration/definition/` endpoint.

=== "Request"

    ```HTTP hl_lines="1"
    GET /inspiration/definition/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    Tenant-id: {your_identifier}
    Authorization: Bearer {access_token_string}
    ```

=== "Response (body)"

    ```JSON hl_lines="10"
    [{
        "name": "str: The formatted full name of your inspiration, e.g. Top Financial Health",
        "id": "str: The short identifier name, use underscore seperor, e.g. top_financial_health",
        "description": "str: Description of the inspiration, e.g. Financially healthy companies.",
        "type": "str: InvestSuite Thematic-datatype field (inspiration|theme|metatheme).",
        "custom": "bool: Custom to tenant or whitelabel (false|true)",
        "query": {
            "xray": "$FINANCIAL_HEALTH_STARS >= 0"
        },
    },
    ...
    ]
    ```

<!-- ## Inspiration Definition specification
TODO add more info on query language and potential datasource targets. -->
