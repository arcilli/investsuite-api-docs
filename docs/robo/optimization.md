---
title: Managing Optimizations
---

!!! info "Definition"

    See the [Glossary](../concepts/glossary.md#optimization-of-a-portfolio) for a definition.

## Get the latest optimization of a portfolio

The `portfolio_update` section within an optimisation object can be configured on client level to return the quantity of instruments that need to be bought or sold in either `UNITS` or in `AMOUNT`.

* Option `UNITS`: for each instrument the quantity is set in *units* to be bought or sold (see the example above)

* Option `AMOUNT`: for each instrument the quantity is set as an *amount* to be bought or sold EXCEPT when the position should be sold entirely. In that case, the quantity type for that position will be delivered in UNITS.

<!-- roald dixit we will hit the wall once the universe is split betw fractional an non fractional (ETF not in amount). Maarten Wyns has this in the backlog -->

=== "UNITS (Default)"

    ```HTTP
    GET /portfolios/{id}/optimizations/current HTTP/1.1
    Content-Type: application/json

    {
        "id": "O01ARZ3NDEKTSV4RRFFQ69G5FAV",

        ...
        
        "portfolio_update":
        {
            "is_recommended": true,
            "orders": 
            {
                "IE00B44Z5B48": // Buy 5 units IE00B44Z5B48
                {
                    "quantity_type": "UNITS",
                    "quantity": 5,
                    "shares": 5,
                    "expected_share_price": 123.45,
                    "expected_transaction_cost": 25
                },
                "LU0378818131": // Partial or full sell LU0378818131
                {
                    "quantity_type": "UNITS",
                    "quantity": -8,
                    "shares": -8,
                    "expected_share_price": 54.32,
                    "expected_transaction_cost": 35
                }
            
            }
        }
    }
    ```

=== "AMOUNT"

    ```HTTP
    GET /portfolios/{id}/optimizations/current HTTP/1.1
    Content-Type: application/json

    {
        "id": "O01ARZ3NDEKTSV4RRFFQ69G5FAV",

        ...
        
        "portfolio_update":
        {
            "is_recommended": true,
            "orders": 
            {
                "IE00B44Z5B48": // Buy
                {
                    "quantity_type": "AMOUNT",
                    "quantity": 617.25,
                    "shares": 5,
                    "expected_share_price": 123.45,
                    "expected_transaction_cost": 25
                },
                "LU0378818131": // Partial Sell
                {
                    "quantity_type": "AMOUNT",
                    "quantity": -434.56,
                    "shares": -8,
                    "expected_share_price": 54.32,
                    "expected_transaction_cost": 35
                },
                "LU0378818131": // Full Sell
                {
                    "quantity_type": "UNITS",
                    "quantity": -11,
                    "shares": -11,
                    "expected_share_price": 54.32,
                    "expected_transaction_cost": 35
                }
            }
        }
    }
    ```
