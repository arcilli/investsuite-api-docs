---
title: Quick start
---

## Introduction

Get acquainted with the InvestSuite API in no time by going from scratch to an optimised portfolio. This quick start takes you through the steps typically pursued when integration with InvestSuite's Optimizer and Robo Advisor products. Below sequence diagram describes these steps:

1. Create a user by invoking `POST /users/`.
2. Create a portfolio by invoking `POST /portfolios/`.
3. Fund the account linked to the portfolio. This is between you and your broker or order management system.
4. Update the portfolio with the portfolio holdings you get from the broker (initially only a cash holding).
5. Get order recommendations from the Optimizer, based on the holdings and portfolio settings.
6. Place the order with your broker.
7. Post the transactions you get back from your broker.
8. Repeat steps 4 - 7.

## Integration flow

![Optimizer Integration](../img/investsuite_optimizer_api_integration.jpg)

Ready to get your hands dirty? Let's go 🕹 ...

## Steps

### 1. Create a user

Create a user for your customer so that in the next step you can define that user as owner of a portfolio. Optionally add an e-mail address and phone number to add the user to the underlying Identity Provider so that your customer can log in to a front-end to view and manage portfolios. 

=== "Request"

    ```HTTP hl_lines="1"
    POST /users/ HTTP/1.1
    Host: api.uat.investsuite.com
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    Content-Type: application/json
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJ...

    {
        "external_id": "unique_external_entity_id",
        "first_name": "Ashok",
        "last_name": "Kumar",
        "email": "ashok.kumar@example.com",
        "phone": "+123456789"
    }

    ```

=== "Response (body)"

    ```JSON
    {
        "external_id": "unique_external_entity_id",
        "id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
        "creation_datetime": "2021-02-18T08:21:02+00:00",
        "version": 3,
        "version_datetime": "2021-02-18T08:21:02+00:00",
        "version_authored_by_user_id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
        "deleted": false,
        "first_name": "Ashok",
        "last_name": "Kumar",
        "email": "ashok.kumar@example.com",
        "phone": "+123456789"
    }
    ```

👆 Copy the User ID from the Response Body to use in the next step.

### 2. Create a portfolio

Create a portfolio under a discretionary mandate. This sort of mandate comprehends that the portfolio is fully managed by the Robo Advisor, without the need for your customer to intervene. Make it a “virtual portfolio” meaning paper money is used to simulate trades. Assign the user you just created as owner. Select the applicable investment policy as management setting required as input to the Optimizer's algorithm.

=== "Request"

    ```HTTP hl_lines="1"
    POST /portfolios/ HTTP/1.1
    Host: api.uat.investsuite.com
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    Content-Type: application/json
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJ...

    {
        "currency":"USD",
        "config":{
            "manager":"ROBO_ADVISOR_DISCRETIONARY",
            "manager_version":1
            "manager_settings": {
                "policy_id":"Y01EF46X9XB437JS4678X0K529C",
            }
        },
        "external_id":"your-bank-portfolio-1",
        "money_type":"PAPER_MONEY",
        "owned_by_user_id":"U01F5WYKRRXZHXT9S6FF1JZNJVZ",
    }

    ```

=== "Response (body)"

    ```JSON
    {
        "external_id": "your-bank-portfolio-1",
        "owned_by_user_id": "U01F5WYKRRXZHXT9S6FF1JZNJVZ",
        "currency": "USD",
        "money_type": "PAPER_MONEY",
        "config":{
            "manager": "ROBO_ADVISOR_DISCRETIONARY",
            "manager_version":1,
            "manager_settings": {
                "policy_id": "Y01EF46X9XB437JS4678X0K529C",
                "active": true
            }
        },
        "snapshot_datetime": null,
        "funded_since": null,
        "id": "P01F8ZSNV0J45R9DFZ3D7D8C26F",
        "creation_datetime": "2021-06-24T19:59:15.474241+00:00",
        "version": 1,
        "version_datetime": "2021-06-24T19:59:15.474241+00:00",
        "version_authored_by_portfolio_id": "U01EJQSYGYQJJ5GNFM4ZXW59Q0X",
        "deleted": false,
        "status": "WAITING_FOR_FUNDS"
    }
    ```

👆 Copy the Portfolio ID from the Response Body to use in the next step.

### 3. Fund the portfolio

Add an initial amount for the Robo Advisor to invest the portfolio you just created. To indicate a portfolio's holding to be the cash holding use the portfolio `currency` abbreviation defined in the ISO international standard 4217, e.g. AUD, and prefix it with the $-sign so `$AUD`.

=== "Request"

    ```HTTP hl_lines="1"
    PATCH /portfolios/P01F8ZSNV0J45R9DFZ3D7D8C26F/ HTTP/1.1
    Host: api.uat.investsuite.com
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    Content-Type: application/json
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJ...

    {
        "holdings": {
            "$USD":10000
        }
    }

    ```

=== "Response (body)"

    ```JSON
    {
        "external_id": "your-bank-portfolio-1",
        "owned_by_user_id": "U01F5WYKRRXZHXT9S6FF1JZNJVZ",
        "currency": "USD",
        "money_type": "PAPER_MONEY",
        "config":{
            "manager": "ROBO_ADVISOR_DISCRETIONARY",
            "manager_version":1,
            "manager_settings": {
                "policy_id": "Y01EF46X9XB437JS4678X0K529C",
                "active": true
            }
        },
        "holdings": {
            "$USD":10000
        },
        "snapshot_datetime": null,
        "funded_since": null,
        "id": "P01F8ZSNV0J45R9DFZ3D7D8C26F",
        "creation_datetime": "2021-06-24T19:59:15.474241+00:00",
        "version": 2,
        "version_datetime": "2021-06-24T19:59:15.474241+00:00",
        "version_authored_by_portfolio_id": "U01EJQSYGYQJJ5GNFM4ZXW59Q0X",
        "deleted": false,
        "status": "ACTIVE"
    }
    ```

### 4. Get order recommendations

Given you assigned a policy and an initial amount to the portfolio as part of the two previous steps, you can now issue a `GET` request to retrieve order recommendations for the portfolio you just initiated.

=== "Request"

    ```HTTP hl_lines="1"
    GET /portfolios/P01F8ZSNV0J45R9DFZ3D7D8C26F/optimization/ HTTP/1.1
    Host: api.uat.investsuite.com
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    Content-Type: application/json
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJ...
    ```

=== "Response (body)"

    ```JSON
    {
    "current_solution":{
        "objective_value":-2.3899945188058256e-05,
        "portfolio":{
            "US78468R1014":3,
            "$USD":402.52
        },
        "look_through":{
            "asset_classes":{
                "alternatives":0.0,
                "bonds":1.0,
                "commodities":0.0,
                "stocks":0.0,
                "cash":0.0
            },
            "regions":{
                "bonds":{
                "asia_pacific_developed":0.0,
                "emerging":0.0,
                "europe_developed":0.0,
                "north_america":1.0
                },
                "stocks":{
                "asia_pacific_developed":0.0,
                "emerging":0.0,
                "europe_developed":0.0,
                "north_america":0.0
                }
            },
            "bond_types":null,
            "sectors":{
                "basic_materials":0.0,
                "consumer_cyclical":0.0,
                "consumer_defensive":0.0,
                "communication_services":0.0,
                "energy":0.0,
                "financial_services":0.0,
                "healthcare":0.0,
                "industrials":0.0,
                "real_estate":0.0,
                "technology":0.0,
                "utilities":0.0
            }
        }
    },
    "optimal_solution":{
        "objective_value":0.001359509844724587,
        "portfolio":{
            "US4642886612":0.76,
            "US78468R1014":3,
            "US4642863926":0.7381,
            "US46429B2676":3.7190000000000003,
            "US78468R2004":3.0000000000000004,
            "$USD":14.769662844999981
        },
        "look_through":{
            "asset_classes":{
                "alternatives":0.0,
                "bonds":0.7986485447656201,
                "commodities":0.0,
                "stocks":0.20135145523437992,
                "cash":0.0
            },
            "regions":{
                "bonds":{
                "asia_pacific_developed":0.0,
                "emerging":0.0,
                "europe_developed":0.0,
                "north_america":1.0
                },
                "stocks":{
                "asia_pacific_developed":0.09369676320272571,
                "emerging":0.0,
                "europe_developed":0.1799403747870528,
                "north_america":0.7263628620102215
                }
            },
            "bond_types":null,
            "sectors":{
                "basic_materials":0.046054619609495716,
                "consumer_cyclical":0.13806308600212508,
                "consumer_defensive":0.07094628258129534,
                "communication_services":0.022473042495950425,
                "energy":0.03446538322470511,
                "financial_services":0.16124155877170626,
                "healthcare":0.12224125679967916,
                "industrials":0.11992341101283713,
                "real_estate":0.0,
                "technology":0.2563740870896863,
                "utilities":0.02821727241251957
            }
        },
    },
    "portfolio_update":{
        "is_recommended":true,
        "orders":{
            "US4642886612":{
                "shares":0.76,
                "expected_share_price":130.09,
                "expected_transaction_cost":0.4943420000000001
            },
            "US4642863926":{
                "shares":0.7381,
                "expected_share_price":130.31,
                "expected_transaction_cost":0.48090905500000003
            },
            "US46429B2676":{
                "shares":3.719,
                "expected_share_price":26.58,
                "expected_transaction_cost":0.4942551
            },
            "US78468R2004":{
                "shares":3,
                "expected_share_price":30.64,
                "expected_transaction_cost":0.4596
            }
        }
    },
    "id":"O01FGNGNS3R3836WB3JHD22J748",
    "creation_datetime":"2021-09-28T06:15:06.613287+00:00",
    "version":1,
    "version_datetime":"2021-09-28T06:15:06.613287+00:00",
    "version_authored_by_user_id":"UXXXXXXXXXXXXXXXXXXXXXXXXXX",
    "deleted":false,
    }   
    ```
