---
title: Managing Optimizations
---

!!! info "Definition"

    See the [Glossary](../concepts/glossary.md#optimization-rebalancing-of-a-portfolio) for a definition.

## Context

This page lists all operations that can be performed on the Optimization object, see [Glossary](../concepts/glossary.md#optimization-rebalancing-of-a-portfolio).
## Concepts

### Object structure

The following image shows the object structure:

- Some metadata: id, version, ...
- The assessment of the as-is Portfolio (`current_solution`) and the recommended Portfolio (`optimal_solution`)
- The delta between the two, ie. what orders to place with the broker to get to the recommended Portfolio
- *Note that the `look_through` has been omitted in the image*

![alt](optimization.png)

<!-- Embed of -- if this gets solved https://github.com/AykutSarac/jsoncrack.com/issues/171

{"current_solution":{"objective_value":-2.3899945188058256e-05,"portfolio":{"BE78468R1014":3,"$USD":402.52},"look_through":{}},"optimal_solution":{"objective_value":0.001359509844724587,"portfolio":{"BE4642886612":0.76,"BE78468R1014":3,"BE4642863926":0.7381,"BE46429B2676":3.7190000000000003,"BE78468R2004":3.0000000000000004,"$USD":14.769662844999981},"look_through":{}},"portfolio_update":{"is_recommended":true,"orders":{"BE4642886612":{"shares":0.76,"expected_share_price":130.09,"expected_transaction_cost":0.4943420000000001},"BE4642863926":{"shares":0.7381,"expected_share_price":130.31,"expected_transaction_cost":0.48090905500000003},"BE46429B2676":{"shares":3.719,"expected_share_price":26.58,"expected_transaction_cost":0.4942551},"BE78468R2004":{"shares":3,"expected_share_price":30.64,"expected_transaction_cost":0.4596}}},"id":"O01FGNGNS3R3836WB3JHD22J748","creation_datetime":"2021-09-28T06:15:06.613287+00:00","version":1,"version_datetime":"2021-09-28T06:15:06.613287+00:00","version_authored_by_user_id":"UXXXXXXXXXXXXXXXXXXXXXXXXXX","deleted":false}

 -->

 The raw JSON (including `look_through`):

```JSON
--8<-- "robo/optimization.get-latest-optimization.response.http"
```

## Create optimization

See the [Rebalancing](rebalancing.md#1-optimization-is-triggered) process to understand the triggers.

## Get optimization

### Get the latest optimization of a portfolio

The `portfolio_update` section within an optimisation object can be configured on client level to return the quantity of instruments that need to be bought or sold in either `UNITS` or in `AMOUNT`:

* `UNITS` (default): for each instrument the quantity is set in *units* to be bought or sold.

* `AMOUNT`: for each instrument the quantity is set as an *amount* to be bought or sold EXCEPT when the position should be sold entirely. In that case, the quantity type for that position will be delivered in `UNITS`.

<!-- roald dixit we will hit the wall once the universe is split betw fractional an non fractional (ETF not in amount). Maarten Wyns has this in the backlog -->

=== "Request"

    ```HTTP
    --8<-- "robo/optimization.get-latest-optimization.request.http"
    ```

=== "Response (Units)"

    ```JSON
    {
        "id": "P01F8ZSNV0J45R9DFZ3D7D8C26F",

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

=== "Response (Amount)"

    ```JSON
    {
        "id": "P01F8ZSNV0J45R9DFZ3D7D8C26F",

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

#### Optimization of an already optimal portfolio

```JSON linenums="1" hl_lines="9"
--8<-- "robo/optimization.get-latest-optimization-no-optimizations.response.http"
```


<!-- ## Update optimization

## Delete optimization

## Query optimization -->