---
title: Manage portfolios
---

!!! info "Definition"

    See the [Glossary](../concepts/glossary.md#portfolio-of-a-user) for a definition.

## Context

This page lists all operations that can be performed on the Portfolio object, see See [Glossary](glossary.md).

<!-- ## Concepts

### Status

|  | Description | Self Investor Portfolio | Robo Advisor Portfolio |
|---|---|---|---|
| DRAFT | The user is required to complete some action still, such as completing onboarding, signing documents, etc. | - | Y |
| VIRTUAL PORTFOLIO | Portfolio that looks and behaves like an active portfolio, but that is funded with paper money | - | Y |
| ACTIVATING | User has completed everything needed, and needs to wait for their account(s) to be activated at the broker/custodian and the bank |  |  |
| WAITING_FOR_FUNDS | Robo Advisor: Less than the minimum amount has been invested, and the portfolio needs more money before becoming active |  |  |
| WAITING_FOR_INVESTMENTS |  |  |  |
| ACTIVE | Live portfolios that are funded (initial cash and/or securities deposit has happened) |  |  |
| BLOCKED | Blocked account instructed manually |  |  |
| ARCHIVED | Portfolio is archived in the sense that it is no longer active for user action, but client still has access to the history, e.g. for 1 year. This occurs when the user has requested that the account/portfolio be closed. |  |  |
| REMOVED | Portfolio is removed from app, only viewable for Legal purposes (15 years retention period, legally required property rights.) |  |  |
| FORGOTTTEN |  |  |  | -->
<!-- Source: https://docs.google.com/spreadsheets/d/1_b1WQl1M6H1orTxWt71RJDWs280biPth-f-1y-rVeGA/edit?pli=1#gid=0 -->


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

=== "Request"

    ```HTTP hl_lines="11 13"
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
        "money_type":"REAL_MONEY",
        "config":{
            "manager":"USER_MANAGED",
            "manager_version":1,
            "manager_settings":{
            }
        },
        "brokerage_account":{
            "bank_account_type":"IBAN",
            "bank_account_number":"BE01234567891234"
            "payment_reference":"32154796"
        }
    }
    ```

!!! warning

    Self Investor only supports `REAL_MONEY` as `money_type` 

Field | Description | How to provide
--- | --- | ---
brokerage_account > bank_account_type | Type of the bank account number, typically an IBAN number. | request body
brokerage_account > bank_account_number | Account number of the account to which the user should transfer money for funding the portfolio. This can be a pooled account or an account specifically for the portfolio. Required if the user uses InvestSuite's front-end applications for investing. Displayed in the funding screen of the InvestSuite app. | request body
brokerage_account > payment_reference | Payment reference the user needs to add to the bank transfer for funding the portfolio. Required if the user uses InvestSuite's front-end applications for investing and the portfolio is funded through a pooled account. Displayed in the funding screen of the InvestSuite app. | request body

<!-- TODO clarify payment reference etc omnibus account -->

Explore the 'User Managed Portfolio settings' in `config.manager_settings` in the [API documentation](https://api.sandbox.investsuite.com/redoc#operation/create_portfolios__post) for all available options.

### Typical Robo Advisor portfolio

A typical Robo Advisor portfolio also has a Goal, Horizon and Policy defined. See [the Robo Advisor section](../robo/introduction.md) for more information on these.

=== "Request"

    ```HTTP hl_lines="12 13 14"
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

### Holdings

To update the holdings you patch the `portfolio` field in the Portfolio object.

!!! warning "Consistency"

    Holdings are not updated when Transactions are created in the Portfolio. The middleware is responsible of keeping these consistent. This is by design to allow flexibility.

!!! warning "Nested object"

    As with all nested objects, include the full object (ie. all holdings) when issuing the PATCH.

=== "Request"

    ```HTTP hl_lines="1 8"
    PATCH /portfolios/P01F8ZSNV0J45R9DFZ3D7D8C26F/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    Content-Type: application/json
    Authorization: Bearer {string}

    "portfolio": {
        "$USD": 208.086729,
        "LU78464A6644": 18.78,
        "LU4642886612": 9.2243,
        "LU46137V2410": 9,
        "LU3160923039": 3,
        "LU46429B2676": 45.146,
        "LU46434V7617": 9,
        "LU4642865251": 7.6828,
        "LU46434V4234": 3
    }
    ```

=== "Response (body)"

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
        "portfolio": {
            "$USD": 208.086729,
            "LU78464A6644": 18.78,
            "LU4642886612": 9.2243,
            "LU46137V2410": 9,
            "LU3160923039": 3,
            "LU46429B2676": 45.146,
            "LU46434V7617": 9,
            "LU4642865251": 7.6828,
            "LU46434V4234": 3
        },
        "snapshot_datetime": null,
        "funded_since": null,
        "id": "P01F8ZSNV0J45R9DFZ3D7D8C26F",
        "creation_datetime": "2021-06-24T19:59:15.474241+00:00",
        "version": 3,
        "version_datetime": "2021-06-24T19:59:15.474241+00:00",
        "version_authored_by_portfolio_id": "U01EJQSYGYQJJ5GNFM4ZXW59Q0X",
        "deleted": false,
        "status": "ACTIVE"
    }
    ```

### Funded Status

On the first funding of a Portfolio, the `funded_since` field should be set. It may not be changed afterwards. It contains the date and time at which the portfolio was funded with the minimal required funding amount to initialize Robo Advisor.
It is used for performance (eg. TWR) calculations.

<!-- This theoretically also applies to Self Investor, but there we own the broker integration.
For PAPER_MONEY portfolios this is not required. Funding there also does not happen through the middleware
TODO Quid if we don t do it? -->

=== "Request"

    ```HTTP hl_lines="1 9"
    PATCH /portfolios/P01F8ZSNV0J45R9DFZ3D7D8C26F/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    Content-Type: application/json
    Authorization: Bearer {string}

    {
        "funded_since": "2021-02-18T08:21:02+00:00"
    }
    ```

### Transactions

See [Transactions](transactions.md).

### Manager settings

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

### Brokerage account

Brokerage Account: see [Glossary](glossary.md#accounts).

=== "Request"

    ```HTTP hl_lines="9 10"
    PATCH /portfolios/P1234JSJDFJSIDDKJDI2334J3IJ/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    Content-Type: application/json
    Authorization: Bearer {string}

    "brokerage_account": {
        "bank_account_type": "IBAN",
        "bank_account_number": "BE19000000001212",
        "bank_id": null,
        "payment_reference": null
    }
    ```

=== "Response (body)"

    ```JSON hl_lines="41 42"
    {
    "external_id": "your-external-id",
    "readable_by": [
        "U1234JSJDFJSIDDKJDI2334J3IJ"
    ],
    "modifiable_by": [
        "U1234JSJDFJSIDDKJDI2334J3IJ"
    ],
    "name": "Child's education",
    "owned_by_user_id": "U1234JSJDFJSIDDKJDI2334J3IJ",
    "base_currency": "USD",
    "money_type": "REAL_MONEY",
    "portfolio": {},
    "config": {
        "manager": "ROBO_ADVISOR_DISCRETIONARY",
        "manager_version": 1,
        "manager_settings": {
            "broker_provider": null,
            "policy_id": "Y01ESJVZJBHJKPQWQACBD8X9M3Z",
            "goal_id": "L01ESJVTZCZBG1E9G7545N4H3TX",
            "horizon_id": "H01ESJVWTJDJPG4E2C83GSY4JHB",
            "risk_profile_id": "K01ESJVY0XE6VM1H97DZRAFCHM1",
            "divest_amount": null,
            "start_amount": 2000,
            "recurring_deposit_amount": 500,
            "end_datetime": null,
            "active": true,
            "suitability_profile_id": null,
            "proposed_risk_profile_id": "K01ESJVY0XE6VM1H97DZRAFCHM1",
            "tags": [],
            "risk_profile_score": 3,
            "background_image": "image-url",
            "background_color": null,
            "commission_profile": null,
            "optimization_cooldown_end": null
        }
    },
    "brokerage_account": {
        "brokerage_portfolio_id": null,
        "brokerage_user_id": null,
        "bank_account_type": "IBAN",
        "bank_account_number": "BE19000000001212",
        "bank_id": null,
        "payment_reference": null
    },
    "funded_since": null,
    "owned_by_user_ids": [],
    "snapshot_datetime": "2022-08-18T05:14:31.904742+00:00",
    "archived": false,
    "start_datetime": "2022-07-26T14:31:06.976215+00:00",
    "id": "P1234JSJDFJSIDDKJDI2334J3IJ",
    "creation_datetime": "2022-07-26T14:31:06.976215+00:00",
    "version": 15,
    "version_datetime": "2022-08-18T05:14:31.926372+00:00",
    "version_authored_by_user_id": "UXXXXXXXXXXXXXXXXXXXXXXXXXX",
    "deleted": false,
    "status": "WAITING_FOR_FUNDS",
     }
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`brokerage_account` | Account information associated with this portfolio | `Object` |  | no
`brokerage_account->bank_account_number` | Account number of the portfolio owner's bank account associated with this portfolio for payment instructions. | `string ^[A-Z]{2}[A-Z0-9]{14,30}\Z` | BE01234567891234 | yes
`brokerage_account->bank_account_type` | Type of the bank account number that is associated with this portfolio, typically an IBAN number. | `enum("ABA", "IBAN")` | IBAN | yes
`brokerage_account->bank_id` | Bank identifier code or ID of the bank used for routing instructions, typically a BIC identifier. | `string AnyOf("^[0-9]{9}\Z", "^[A-Z]{4}[A-Z]{2}[A-Z0-9]{2}([A-Z0-9]{3})?\Z")` | IDQMIE2D | no
`payment_reference` | Needed when all customers need to wire money to the same bank account | `string AnyOf("^[0-9]{9}\Z", "^[A-Z]{4}[A-Z]{2}[A-Z0-9]{2}([A-Z0-9]{3})?\Z")`  |  | no

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

You can query each entity through a general endpoint e.g. `GET /portfolios/?query=…`. Learn more in the [Handling collection responses](entities.md#querying-business-objects) section.

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