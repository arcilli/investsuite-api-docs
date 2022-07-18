---
title: Manage portfolios
---

## Context

This page lists all operations that can be performed on the Portfolio object, see See [Glossary](../getting_started/glossary.md).
## Create a portfolio
### Minimum portfolio

=== "Request"

    ```HTTP
    POST /portfolios/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    Content-Type: application/json
    Authorization: Bearer {string}
    {
        "name":"General investing",
        "owned_by_user_id":"U01F5WYKRRXZHXT9S6FF1JZNJVZ",
        "base_currency":"USD",
        "money_type":"PAPER_MONEY",
        "config":{
            "manager":"ROBO_ADVISOR_DISCRETIONARY",
            "manager_version":1,
            "manager_settings":{
            }
        }
    }
    ```

=== "Response (body)"

    ```JSON
    {
        "name":"General investing",
        "owned_by_portfolio_id":"U01F5WYKRRXZHXT9S6FF1JZNJVZ",
        "base_currency":"USD",
        "money_type":"PAPER_MONEY",
        "config":{
            "manager":"ROBO_ADVISOR_DISCRETIONARY",
            "manager_version":1,
            "manager_settings":{
                "active":true
            }
        },
        "brokerage_account":null,
        "archived":false,
        "funded_since":null,
        "id":"P01F8ZSNV0J45R9DFZ3D7D8C26F",
        "creation_datetime":"2021-06-24T19:59:15.474241+00:00",
        "version":1,
        "version_datetime":"2021-06-24T19:59:15.474241+00:00",
        "version_authored_by_portfolio_id":"U01EJQSYGYQJJ5GNFM4ZXW59Q0X",
        "deleted":false,
        "status":"ACTIVE"
    }
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`name` | The display name of the portfolio set by the user. | `string <= 128 characters` | My Portfolio-1 | yes
`owned_by_user_id` | The portfolio owner's user ID. This field is required firstly to get to the account of the owner for cash withdrawals, and secondly to group portfolios by owner. | `string ^U[0-9A-HJKMNP-TV-Z]{26}\Z` | U01ARZ3NDEKTSV4RRFFQ69G5FAV | yes
`base_currency` | The portfolio's currency. | `string ^[A-Z]{3}\Z` | USD | yes
`money_type` | Defines whether this is a 'virtual' portfolio or not. In a virtual portfolio buying and selling decisions are simulated, rather than placed as actual orders through a broker. ! Write once | `enum("REAL_MONEY", "PAPER_MONEY")` | REAL_MONEY | yes
`config->manager` | The manager type is either: self managed, or managed by the Robo Advisor under an advisory or discretionary mandate. For **Self Investor** select `USER_MANAGED` ! Write once. | `enum("USER_MANAGED", "ROBO_ADVISOR_ADVISORY", "ROBO_ADVISOR_DISCRETIONARY")` | ROBO_ADVISOR_DISCRETIONARY | yes
`config->manager_version` | Which major version of the selected portfolio manager to use. | `integer >= 1` | 1 | yes

!!! warning

    The `money_type` cannot be changed: a paper money portfolio cannot be converted to a real money portfolio (by design).

    This has implications when portfolios are created programmatically, for example during onboarding. Special care is needed when the ID is stored in the middleware.

### Typical Self Investor portfolio

See the [example above](#minimum-portfolio), but with the `config.manager` set to `USER_MANAGED`.

Explore the 'User Managed Portfolio settings' in `config.manager_settings` in the [API documentation](https://api.sandbox.investsuite.com/redoc#operation/create_portfolios__post) for all available options.

### Typical Robo Advisor portfolio

A typical Robo Advisor portfolio also has a Goal, Horizon and Policy defined. See [the Robo Advisor section](../robo/introduction.md) for more information on these.

=== "Request"

    ```HTTP
    POST /portfolios/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    Content-Type: application/json
    Authorization: Bearer {string}
    {
        "base_currency":"USD",
        "config":{
            "manager":"ROBO_ADVISOR_DISCRETIONARY",
            "manager_settings":{
                "goal_id":"L01EF46X4872VVN0QRW4XF2ZP6W",
                "horizon_id":"H01EQ3429CY6Y2NW0ZF8A8Y2FYJ",
                "policy_id":"Y01EF46X9XB437JS4678X0K529C"
            },
            "manager_version":1
        },
        "external_id":"your-bank-portfolio-1",
        "money_type":"PAPER_MONEY",
        "name":"General investing",
        "owned_by_user_id":"U01F5WYKRRXZHXT9S6FF1JZNJVZ",
        "portfolio":{
            "$USD":10000
        },
    }
    ```

=== "Response (body)"

    ```JSON
    {
        "external_id":"your-bank-portfolio-1",
        "name":"General investing",
        "owned_by_portfolio_id":"U01F5WYKRRXZHXT9S6FF1JZNJVZ",
        "base_currency":"USD",
        "money_type":"PAPER_MONEY",
        "config":{
            "manager":"ROBO_ADVISOR_DISCRETIONARY",
            "manager_version":1,
            "manager_settings":{
                "policy_id":"Y01EF46X9XB437JS4678X0K529C",
                "goal_id":"L01EF46X4872VVN0QRW4XF2ZP6W",
                "horizon_id":"H01EQ3429CY6Y2NW0ZF8A8Y2FYJ",
                "active":true
            }
        },
        "brokerage_account":null,
        "portfolio":{
            "$USD":10000
        },
        "snapshot_datetime":null,
        "archived":false,
        "funded_since":null,
        "id":"P01F8ZSNV0J45R9DFZ3D7D8C26F",
        "creation_datetime":"2021-06-24T19:59:15.474241+00:00",
        "version":1,
        "version_datetime":"2021-06-24T19:59:15.474241+00:00",
        "version_authored_by_portfolio_id":"U01EJQSYGYQJJ5GNFM4ZXW59Q0X",
        "deleted":false,
        "status":"ACTIVE"
    }
    ```

Explore the 'Robo Advisor Managed portfolio settings' in `config.manager_settings` in the [API documentation](https://api.sandbox.investsuite.com/redoc#operation/create_portfolios__post) for all available options.

## Get a portfolio

Add the InvestSuite ID to the path to retrieve a portfolio object.

=== "Request"

    ```HTTP hl_lines="1"
    GET /portfolios/P01F8ZSNV0J45R9DFZ3D7D8C26F HTTP/1.1
    Host: api.sandbox.investsuite.com
    Authorization: Bearer {string}
    ```

=== "Response (body)"

    ```JSON hl_lines="10"
    {
        "external_id":"your-bank-portfolio-1",
        "name":"General investing",
        "owned_by_portfolio_id":"U01F5WYKRRXZHXT9S6FF1JZNJVZ",
        "base_currency":"USD",
        "money_type":"PAPER_MONEY",
        "config":{
            "manager":"ROBO_ADVISOR_DISCRETIONARY",
            "manager_version":1,
            "manager_settings":{
                "policy_id":"Y01EF46X9XB437JS4678X0K529C",
                "goal_id":"L01EF46X4872VVN0QRW4XF2ZP6W",
                "horizon_id":"H01EQ3429CY6Y2NW0ZF8A8Y2FYJ"
            }
        },
        "portfolio":{
            "$USD":10000
        },
        "snapshot_datetime":null,
        "archived":false,
        "funded_since":null,
        "id":"P01F8ZSNV0J45R9DFZ3D7D8C26F",
        "creation_datetime":"2021-06-24T19:59:15.474241+00:00",
        "version":1,
        "version_datetime":"2021-06-24T19:59:15.474241+00:00",
        "version_authored_by_portfolio_id":"U01EJQSYGYQJJ5GNFM4ZXW59Q0X",
        "deleted":false,
        "status":"ACTIVE"
    }
    ```

## Update a portfolio

Given the right permissions you can update any object by issuing a `PATCH` request.

!!! warning
    
    The following fields cannot be updated: 
    
    - The `manager` field defines the type of portfolio management: self directed, under an advisory mandate, under a discretionary mandate.
    - The `money_type` field states if the portfolio manages real money or paper money.

### Update the portfolio manager settings

Below table lists the applicable portfolio manager parameters. The properties can be updated by issueing a corresponding PATCH request (see example below). See the API specification for more information.

=== "Request"

    ```HTTP hl_lines="10"
    PATCH /portfolios/P01F8ZSNV0J45R9DFZ3D7D8C26F/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    Content-Type: application/json
    Authorization: Bearer {string}

    "config": {
        "manager_settings": {
            "policy_id":"Y01EF46X9XB437JS4678X0K529C"
        }
    }
    ```

=== "Response (body)"

    ```JSON hl_lines="10"
    {
        "external_id":"your-bank-portfolio-1",
        "name":"General investing",
        "owned_by_portfolio_id":"U01F5WYKRRXZHXT9S6FF1JZNJVZ",
        "base_currency":"USD",
        "money_type":"PAPER_MONEY",
        "config":{
            "manager":"ROBO_ADVISOR_DISCRETIONARY",
            "manager_version":1,
            "manager_settings":{
                "policy_id":"Y01EF46X9XB437JS4678X0K529C",
                "goal_id":"L01EF46X4872VVN0QRW4XF2ZP6W",
                "horizon_id":"H01EQ3429CY6Y2NW0ZF8A8Y2FYJ"
            }
        },
        "portfolio":{
            "$USD":10000
        },
        "funded_since":null,
        "id":"P01F8ZSNV0J45R9DFZ3D7D8C26F",
        "creation_datetime":"2021-06-24T19:59:15.474241+00:00",
        "version":2,
        "version_datetime":"2021-06-24T19:59:15.474241+00:00",
        "version_authored_by_portfolio_id":"U01EJQSYGYQJJ5GNFM4ZXW59Q0X",
        "deleted":false,
        "status":"WAITING_FOR_FUNDS"
    }
    ```

|  | Robo Advisor Managed Portfolio | User (Self Investor) Managed Portfolio |
|---|---|---|
| broker_provider | ● | ● |
| active | ● | ● |
| tags | ● | ● |
| background_image | ● | ● |
| background_color | ● | ● |
| commission_profile | ● | ● |
| policy_id | ● | - |
| goal_id | ● | - |
| horizon_id | ● | - |
| risk_profile_id | ● | - |
| proposed_risk_id | ● | - |
| risk_profile_score | ● | - |
| suitability_profile_id | ● | - |
| divest_amount | ● | - |
| start_amount | ● | - |
| recurring_deposit_amount | ● | - |
| end_datetime | ● | - |
| optimization_cooldown_end | ● | - |

### Update the brokerage account

Brokerage Account: see [Glossary](../getting_started/glossary.md#accounts).

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`brokerage_account` | Account information associated with this portfolio | `Object` |  | no
`brokerage_account->bank_account_number` | Account number of the portfolio owner's bank account associated with this portfolio for payment instructions. | `string ^[A-Z]{2}[A-Z0-9]{14,30}\Z` | BE01234567891234 | yes
`brokerage_account->bank_account_type` | Type of the bank account number that is associated with this portfolio, typically an IBAN number. | `enum("ABA", "IBAN")` | IBAN | yes
`brokerage_account->bank_id` | Bank identifier code or ID of the bank used for routing instructions, typically a BIC identifier. | `string AnyOf("^[0-9]{9}\Z", "^[A-Z]{4}[A-Z]{2}[A-Z0-9]{2}([A-Z0-9]{3})?\Z")` | IDQMIE2D | no

### Block a portfolio

Portfolio access can be blocked:

- Blocked Self Investor portfolios can no longer trade.
- Blocked Robo Advisor portfolios will no longer be optimized (ie. positions remain as-is and are not rebalanced).

=== "HTTP"

    ```HTTP hl_lines="11"
    PATCH /portfolios/P01F8ZSNV0J45R9DFZ3D7D8C26F/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    Content-Type: application/json
    Authorization: Bearer {string}

    {
        "manager": {
            "manager_settings": {
                active: false
            }
        }
    }

    ```

=== "curl"

    ```bash
    curl -X PATCH \                 
    -H "Content-Type: application/json" \
    -H "Auhorization": "{string}"  \   
    -d '{ "config": { "manager_settings": { active: false } } } }' \
    https://api.sandbox.investsuite.com/portfolios/P01F8ZSNV0J45R9DFZ3D7D8C26F/
    ```

## Delete a portfolio

Given the right permissions you can delete any object by issuing a `DELETE` request.

=== "Request"

    ```HTTP hl_lines="1"
    DELETE /portfolios/P01F8ZSNV0J45R9DFZ3D7D8C26F HTTP/1.1
    Host: api.sandbox.investsuite.com
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    Content-Type: application/json
    Authorization: Bearer {string}
    ```

=== "Response (body)"

    ```JSON
    {
        "external_id":"your-bank-portfolio-1",
        "name":"General investing",
        "owned_by_portfolio_id":"U01F5WYKRRXZHXT9S6FF1JZNJVZ",
        "base_currency":"USD",
        "money_type":"PAPER_MONEY",
        "config":{
            "manager":"ROBO_ADVISOR_DISCRETIONARY",
            "manager_version":1,
            "manager_settings":{
                "policy_id":"Y01EF46X9XB437JS4678X0K529C",
                "goal_id":"L01EF46X4872VVN0QRW4XF2ZP6W",
                "horizon_id":"H01EQ3429CY6Y2NW0ZF8A8Y2FYJ"
            }
        },
        "portfolio":{
            "$USD":10000
        },
        "funded_since":null,
        "id":"P01F8ZSNV0J45R9DFZ3D7D8C26F",
        "creation_datetime":"2021-06-24T19:59:15.474241+00:00",
        "version":2,
        "version_datetime":"2021-06-24T19:59:15.474241+00:00",
        "version_authored_by_portfolio_id":"U01EJQSYGYQJJ5GNFM4ZXW59Q0X",
        "deleted":true,
        "status":"WAITING_FOR_FUNDS"
    }
    ```

## Query portfolios

You can query each entity through a general endpoint e.g. `GET /portfolios/?query=…`. Learn more in the [Handling collection responses](./collections.md) section.

=== "Request"

    ```HTTP hl_lines="2"
    GET /portfolios/
        ?query=external_id+eq+'your-bank-portfolio-1' HTTP/1.1
    Host: api.sandbox.investsuite.com
    Authorization: Bearer {string}
    ```

=== "Response (body)"

    ```JSON hl_lines="10"
    {
        "external_id":"your-bank-portfolio-1",
        "name":"General investing",
        "owned_by_portfolio_id":"U01F5WYKRRXZHXT9S6FF1JZNJVZ",
        "base_currency":"USD",
        "money_type":"PAPER_MONEY",
        "config":{
            "manager":"ROBO_ADVISOR_DISCRETIONARY",
            "manager_version":1,
            "manager_settings":{
                "policy_id":"Y01EF46X9XB437JS4678X0K529C",
                "goal_id":"L01EF46X4872VVN0QRW4XF2ZP6W",
                "horizon_id":"H01EQ3429CY6Y2NW0ZF8A8Y2FYJ",
            }
        },
        "portfolio":{
            "$USD":10000
        },
        "snapshot_datetime":null,
        "funded_since":null,
        "id":"P01F8ZSNV0J45R9DFZ3D7D8C26F",
        "creation_datetime":"2021-06-24T19:59:15.474241+00:00",
        "version":1,
        "version_datetime":"2021-06-24T19:59:15.474241+00:00",
        "version_authored_by_portfolio_id":"U01EJQSYGYQJJ5GNFM4ZXW59Q0X",
        "deleted":false,
        "status":"WAITING_FOR_FUNDS"
    }
    ```