---
title: Managing Optimizations
---

!!! info "Definition"

    See the [Glossary](../concepts/glossary.md#optimization-of-a-portfolio) for a definition.

## Get the latest optimization of a portfolio

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
            "IE00B44Z5B48": 
            {
                "quantity_type": "UNITS",
                "quantity": 5,
                "shares": 5,
                "expected_share_price": 123.45,
                "expected_transaction_cost": 25
            },
            "LU0378818131": 
            {
                "quantity_type": "AMOUNT",
                "quantity": -434.56,
                "shares": -8,
                "expected_share_price": 54.32,
                "expected_transaction_cost": 35
            }
        
        }
    }
}
```