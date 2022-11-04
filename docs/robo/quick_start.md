---
title: Quick start
---

## Context

This page will take you through the typical steps to integrate with InvestSuite's Robo Advisor. Below sequence diagram describes the basic happy path:

- Authenticating to our API
- Onboarding a user on our platform
- Funding a portfolio
- Optimizing (rebalancing) the portfolio
- Getting the orders from this optimization
- Updating the portfolio once these orders have been placed at the broker

!!! info "Postman collection"
    These steps are also available as a Postman collection. Get in touch with us through your sales contact.

For an exhaustive integration solution (including non-happy path), see the  example [middleware design](middleware_design.md).

## Overview

```plantuml source="docs/robo/quick_start.puml"
```

## Authentication phase

### 1. Authenticate

We follow the standard OpenID Connect schema. See [Authentication](../concepts/authentication.md).

## Onboarding phase

This describes the technical flow. To accomodate various business requirements, see [Onboarding](../scenarios/onboarding.md).

### 2. Create a user

Create a user for your customer so that in the next step you can define that user as owner of a portfolio. See [Users](../concepts/users.md#create-a-user).

```HTTP
--8<-- "concepts/users.post-typical.request.http"
```

Save the `user.id` from the response body to use in the next step.

### 3. Create a portfolio

**Get a policy**

To optimize a portfolio it has to reference a _policy_: an investment strategy defined by the bank. Such strategy holds the constraints for the optimization algorithm to take into account when rendering order recommendations, for instance the minimum number of stocks within a certain sector or region. 

Either you already have a `policy.id` or (for the sake of example) get (any) valid `policy.id` by through the following API call, see [Policies](policy.md#query-policies).


```HTTP hl_lines="11"
--8<-- "robo/policy.get-query.request.http"
```

Save the `policy.id` from the response body to use in the next step.

**Create portfolio**

We create a new Robo Advisor Portfolio as follows:

- Specify the discretionary mandate: `config.manager="ROBO_ADVISOR_DISCRETIONARY"`. This sort of mandate comprehends that the portfolio is fully managed by the Robo Advisor, without the need for your customer to intervene. See [Glossary](../concepts/glossary.md#mandate).
- Specify paper money type (as opposed to real money) to simulate trades: `"money_type": "PAPER_MONEY"`. See [Glossary](../concepts/glossary.md#money).
- Specify the user from step 2 as owner: `"owned_by_user_id": "U01F5WYKRRXZHXT9S6FF1JZNJVZ"`.
- Specify the policy as manager setting is required for the Robo Advisor's optimizer algorithm: `config.manager_settings.policy_id="Y01EF46X9XB437JS4678X0K529C"`.

See [Portfolios](../concepts/portfolios.md#typical-robo-advisor-portfolio).

```HTTP
--8<-- "concepts/portfolios.post-typicalrobo.request.http"
```

Response body:

```JSON
{
    "external_id": "your-bank-portfolio-1",
    "owned_by_user_id": "U01F5WYKRRXZHXT9S6FF1JZNJVZ",
    "base_currency": "USD",
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

Save the `portfolio.id` from the response body to use in the next step.

## Funding phase

This describes the technical flow. To accomodate various business requirements, see [Funding](../scenarios/cash_movements.md#broker-integration-by-the-client).

### 4. Fund the portfolio

In this step we're creating a cash transaction, followed by an update to the portfolio holdings. See [Transactions](../concepts/transactions.md#funding).

**Create cash transaction**

```HTTP
POST /portfolios/P01F8ZSNV0J45R9DFZ3D7D8C26F/transactions/ HTTP/1.1
Host: api.sandbox.investsuite.com
Content-Type: application/json
Authorization: Bearer {string}

{
    "external_id": "P01FFMGXDPSZ2HKZD4G55T6YHHD/2014087240",
    "movements": [
        {
            "external_id": "13891096285",
            "type": "CASH_DEPOSIT",
            "status": "SETTLED",
            "datetime": "2021-09-27T00:00:00+00:00",
            "instrument_id": "$USD",
            "quantity": 10000.0,
        }
    ],
}
```

<!-- 
Response body

```JSON
{
    "external_id": "P01FFMGXDPSZ2HKZD4G55T6YHHD/2014087240",
    "type": "CASH_TRANSFER",
    "order_type": null,
    "movements": [
        {
            "external_id": "13891096285",
            "type": "CASH_DEPOSIT",
            "sub_type": "Cash Deposit",
            "description": null,
            "status": "SETTLED",
            "datetime": "2021-09-27T00:00:00+00:00",
            "instrument_id": "$USD",
            "instrument_name": null,
            "quantity": 1
        }
    ],
    "description": null,
    "id": "T01FGNG33GFJK5GCN4KZSPZYMFF",
    "creation_datetime": "2021-09-28T06:04:54.671046+00:00",
    "version": 1,
    "version_datetime": "2021-09-28T06:04:54.671046+00:00",
    "version_authored_by_user_id": "UXXXXXXXXXXXXXXXXXXXXXXXXXX",
    "deleted": false,
    "status": "COMPLETED"
}
``` 
-->

**Update holdings**

Add an initial amount for the Robo Advisor to invest the portfolio you just created. The currency has to match the one defined in the portfolio field `base_currency`.

```HTTP
PATCH /portfolios/P01F8ZSNV0J45R9DFZ3D7D8C26F/ HTTP/1.1
Host: api.sandbox.investsuite.com
Content-Type: application/json
Authorization: Bearer {string}

{
    "portfolio": {
        "$USD": 10000.0
    }
}
```

## Rebalancing phase

This describes the technical flow. To accomodate various business requirements, see [Rebalancing](rebalancing.md).

### 5. Get recommended orders

Funding the Portfolio (or updating the Portfolio's holdings in general) triggers an optimization. A couple of seconds after the previous step, it should be available. See [Optimization](optimization.md).

Issue a `GET` request to retrieve order recommendations:

```HTTP
--8<-- "robo/optimization.get-latest-optimization.request.http"
```

The response body contains various fields, inluding the recommended orders:

```JSON linenums="1" hl_lines="96-117"
--8<-- "robo/optimization.get-latest-optimization.response.http"
```

### 6. Create transactions

Notify Robo Advisor that the orders from the previous step (`portfolio_update.orders`) were placed at the broker, by creating Transactions. See [Transactions](../concepts/transactions.md#order-placed).

<!-- TODO this example is not in line with above -->

```HTTP
POST /portfolios/P01F8ZSNV0J45R9DFZ3D7D8C26F/transactions/ HTTP/1.1
Host: api.sandbox.investsuite.com
Content-Type: application/json
Authorization: Bearer {string}

[
    {
        "movements": [
            {
                "type": "BUY",
                "status": "SETTLED",
                "datetime": "2021-09-24T06:15:01.999300+00:00",
                "instrument_id": "BE4642886612",
                "quantity": 2
            },
            {
                "type": "SELL",
                "status": "SETTLED",
                "datetime": "2021-09-24T06:15:01.999300+00:00",
                "instrument_id": "$USD",
                "quantity": -125
            }
        ]
    },
    {
        "movements": [
            {
                "type": "BUY",
                "status": "SETTLED",
                "datetime": "2021-09-24T06:15:01.930643+00:00",
                "instrument_id": "BE4642863926",
                "quantity": 3
            },
            {
                "type": "SELL",
                "status": "SETTLED",
                "datetime": "2021-09-24T06:15:01.999300+00:00",
                "instrument_id": "$USD",
                "quantity": -350.50
            }
        ]
    }
]
```

### 7. Update holdings

Next update the Portfolio holdings to reflect the bought instrumemts.

```HTTP 
PATCH /portfolios/P01F8ZSNV0J45R9DFZ3D7D8C26F/ HTTP/1.1
Host: api.sandbox.investsuite.com
Accept-Encoding: gzip, deflate
Connection: Keep-Alive
Content-Type: application/json
Authorization: Bearer {string}

"portfolio": {
    "$USD": 9520,
    "BE4642886612": 2,
    "BE4642863926": 3
}
```

This will re-trigger the optimizer. 

When the recommended orders endpoint is queried again, the response should indicate that the Portfolio is now optimal (in line with the investment policy used by the optimizer) returning no recommendations.

```HTTP
--8<-- "robo/optimization.get-latest-optimization.request.http"
```

Response body:

```JSON linenums="1" hl_lines="9"
--8<-- "robo/optimization.get-latest-optimization-no-optimizations.response.http"
```