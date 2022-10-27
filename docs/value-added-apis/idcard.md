---
title: ID Card API
---

## Service Description

Get a list of instrument ID Cards.

ID cards can be retrieved based on either a list of identifiers (ISINS or RICs), or for an entire universe

- [ID Card API Documentation](https://api.data.uat.investsuite.com/redoc#tag/ID-Card)
- [ID Card API Swagger UI Interface](https://api.data.uat.investsuite.com/docs#/ID%20Card/)

## Get ID cards based on a list of identifiers

=== "Curl Request"

    ```bash
    curl -X "GET" \
    "https://api.data.uat.investsuite.com/id-card/query/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    --data-raw '{"instrument_ids": ["A.Z"]}'
    ```

=== "HTTP Request"

    ```HTTP
    GET /id-card/query/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET

    {"instrument_ids": ["A.Z"]}
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`instrument_ids` | The instruments to retrieve ID card data for. Can be either a list of ISINs or a list of RICs. | List[str] | ["A.Z"] | yes

## Get ID cards for an entire universe

=== "Curl Request"

    ```bash
    curl -X "GET" \
    "https://api.data.uat.investsuite.com/id-card/universe/" \
    -H "accept: application/json" \
    -H "Content-Type: application/json" \
    -H "X-TENANT-ID: $TENANT_ID" \
    -H "X-Api-Key: $IVS_API_SECRET" \
    ```

=== "HTTP Request"

    ```HTTP
    GET /id-card/universe/ HTTP/1.1
    Host: api.data.uat.investsuite.com
    accept: application/json
    Content-Type: application/json
    X-TENANT-ID: $TENANT_ID
    X-Api-Key: $IVS_API_SECRET
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`universe_name` | The universe name to retrieve ID card data for. If omitted, data for the entire tenant universe will be returned. | `str` | `robo_universe` | No


After the request, we get the following example response:

=== "Response (Body Content JSON)"
```JSON
{
    "data": {
        "A.Z": {
            "ANALYST_CONSENSUS_PRICE_TARGET": {
                "currency": "USD",
                "price": 149.7857
            },
            "ANALYST_RECOMMENDATIONS": {
                "BUY": 6.0,
                "HOLD": 3.0,
                "OUTPERFORM": 7.0
            },
            "ANALYST_RECOMMENDATION_COUNT": 16.0,
            "ANALYST_UPWARD_POTENTIAL": 25.648603305091843,
            "CURRENCY": "USD",
            "DEBT_EQUITY_LATEST": 56.71613,
            "DESCRIPTION": "Agilent Technologies, Inc. is engaged in providing application-focused solutions that include instruments, software, services and consumables for the entire laboratory workflow. The Company operates in the life sciences, diagnostics and applied chemical markets. Its life sciences and applied markets business provides application-focused solutions that include instruments and software that enable customers to identify, quantify and analyze the physical and biological properties of substances and products. Its diagnostics and genomics business includes the genomics, nucleic acid contract manufacturing and research and development, pathology, companion diagnostics, reagent partnership and biomolecular analysis businesses. Its Agilent CrossLab business spans the entire lab with its range of services portfolio, which is designed to improve customer outcomes. Its product categories include liquid chromatography (LC) systems and components, atomic absorption (AA) instruments and others.",
            "DIVIDEND_PAYOUT_RATIO_TTM": 19.31908,
            "DIV_YIELD_CURRENT": 0.70464,
            "DIV_YIELD_TTM": 0.66437,
            "DPS_PER_FY": null,
            "EPS_GROWTH_LAST_5Y": 22.92492,
            "EPS_TTM": 4.14678,
            "EQUITY_INDUSTRY": "Advanced Medical Equipment & Technology",
            "EQUITY_SECTOR": "Healthcare",
            "FINANCIAL_HEALTH_STARS": 5.0,
            "GROWTH_STARS": 3.0,
            "MARKET_CAP": 35588077791.0,
            "MOMENTUM_STARS": 4.0,
            "NAME": "Agilent Technologies Ord Shs",
            "NET_PROFIT_MARGIN_TTM": 19.35039,
            "NUMBER_OF_EMPLOYEES": 17400.0,
            "PRICE_BOOK_LATEST": 6.95896,
            "PRICE_EARNINGS_EX_EXTRA_TTM": 28.74761,
            "PRICE_SALES_TTM": 5.45564,
            "PROFITABILITY_STARS": 5.0,
            "ROE_TTM": 25.43294,
            "SIZE_LABEL": "LARGE",
            "STABILITY_STARS": 5.0,
            "VALUE_GROWTH_LABEL": "GROWTH",
            "VALUE_STARS": 4.0
        }
        ...
    }
}
```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`data` | Holds all ID Card data. It contains a dictionary with the requested instrument identifiers as keys, and as values dicts with the ID card data fields (see fields below) | `dict` |  | yes
`ANALYST_CONSENSUS_PRICE_TARGET` | Analyst consensus stock price target | dict with currency and price fields | {"currency": "USD", "price": 149.7857} | yes
`ANALYST_RECOMMENDATIONS` | Number of analyst recommendations per recommendation | dict with counts per recommendation | {"BUY": 6.0, "HOLD": 3.0, "OUTPERFORM": 7.0} | yes
`ANALYST_RECOMMENDATION_COUNT` | Total number of analyst recommendations| `float` | 16.0 | yes
`ANALYST_UPWARD_POTENTIAL` | Return (in percent) if the current price would move to the analyst consensus price target | `float` | 25.648603305091843 | yes
`CURRENCY` | Listing currency | `str` | "USD" | yes
`DEBT_EQUITY_LATEST` | Debt to Equity ratio based on latest company filings | `float` | 56.71613 | yes
`DESCRIPTION` | Company description | `str` | "Agilent Technologies, Inc. is engaged in providing application-focused solutions that include instruments, software, services and consumables for the entire laboratory workflow. The Company operates in the life sciences, diagnostics and applied chemical markets. Its life sciences and applied markets business provides application-focused solutions that include instruments and software that enable customers to identify, quantify and analyze the physical and biological properties of substances and products. Its diagnostics and genomics business includes the genomics, nucleic acid contract manufacturing and research and development, pathology, companion diagnostics, reagent partnership and biomolecular analysis businesses. Its Agilent CrossLab business spans the entire lab with its range of services portfolio, which is designed to improve customer outcomes. Its product categories include liquid chromatography (LC) systems and components, atomic absorption (AA) instruments and others." | yes
`DIVIDEND_PAYOUT_RATIO_TTM` | Percentage of earnings that have been paid out as dividend in the last 2 months | `float` | 19.31908 | yes
`DIV_YIELD_CURRENT` | Current company dividend yield (based on latest dividend information) in % | `float` | 0.70464 | yes
`DIV_YIELD_TTM` | Dividends paid out in the last 12 months divided by current price, converted to % | `float` | 0.66437 | yes
`DPS_PER_FY` | Dividends per fiscal year | dict with an amount and currency per fiscal year. null if no dividends were paid out | null | yes
`EPS_GROWTH_LAST_5Y` | Compounded annual EPS growth over the last 5 years (in %) | `float` | 22.92492 | yes
`EPS_TTM` | Earnings per share over the last trailing 12 months | `float` | 4.14678 | yes
`EQUITY_INDUSTRY` | Industry that the stock belongs to (corresponds to the 4th level in the TRBC hierarchy) | `str` | Advanced Medical Equipment & Technology | yes
`EQUITY_SECTOR` | Sector that the stock belongs to (corresponds to the first level in the TRBC hierarchy)  | `str` | Healthcare | yes
`FINANCIAL_HEALTH_STARS` | Number of financial health stars according to the InvestSuite X-Ray calculations | `float` | 5.0 | yes
`GROWTH_STARS` | Number of growth stars according to the InvestSuite X-Ray calculations | `float` | 5.0 | yes
`ISIN` | Instrument ISIN code | `str` | BE0974293251 | yes
`MARKET_CAP` | Company market capitalisation (in the units of CURRENCY) | `float` | 35588077791.0 | yes
`MOMENTUM_STARS` | Number of momentum stars according to the InvestSuite X-Ray calculations | `float` | 4.0 | yes
`NAME` | Instrument name | `str` | Agilent Technologies Ord Shs | yes
`NET_PROFIT_MARGIN_TTM` | Net Profit Margin, calculated as the Income After Taxes for the trailing 12 months divided by Total Revenue for the same period (in %) | `float` | 19.35039 | yes
`NUMBER_OF_EMPLOYEES` | Number of employees | `float` | 17400.0 | yes
`PRICE_BOOK_LATEST` | Latest price to book ratio | `float` | 6.95896 | yes
`PRICE_EARNINGS_EX_EXTRA_TTM` | Price to earnings ratio (based on trailing 12-month earnings), excluding extraordinary items | `float` | 28.74761 | yes
`PRICE_SALES_TTM` | Price to sales ratio (based on trailing 12-month sales) | `float` | 5.45564 | yes
`PROFITABILITY_STARS` | Number of profitability stars according to the InvestSuite X-Ray calculations | `float` | 5.0 | yes
`ROE_TTM` | Return on Equity (ROE), based on trailing 12-month earnings | `float` | 25.43294 | yes
`SIZE_LABEL` | Size Label (LARGE / MID / SMALL) based on InvestSuite's size category methodology | `str` | LARGE | yes
`STABILITY_STARS` | Number of stability stars according to the InvestSuite X-Ray calculations | `float` | 5.0 | yes
`VALUE_GROWTH_LABEL` | Value Growth Label (VALUE / GROWTH) based on InvestSuite's size category methodology | `str` | GROWTH | yes
`VALUE_STARS` | Number of value stars according to the InvestSuite X-Ray calculations | `float` | 4.0 | yes
