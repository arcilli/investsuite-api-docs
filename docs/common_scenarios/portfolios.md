---
title: Create and manage portfolios
---

A portfolio holds one or more investment products for instance stocks, ETFs or cryptocurrency. The composition of a portfolio depends on the investor’s goals, budget, risk appetite and the holding period. An investor can without advise decide what instruments a portfolio is made of. In this case it is a “self managed” portfolio. A portfolio can equally be composed by an algorithm such as the InvestSuite’s iVar algorithm. In that case the portfolio is managed by a Robo Advisor.

Once you have created a portfolio you can create a portfolio and assign the portfolio as owner.

## Create a portfolio

=== "Request"

    ```HTTP hl_lines="1"
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
        "holdings":{
            "$USD":10000
        },
    }

    ```

=== "Response (body)"

    ```JSON
    {
        "external_id":"your-bank-portfolio-1""name":"General investing",
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
        "holdings":{
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

## Search

You can query each entity through a general endpoint e.g. `GET /portfolios/?query=…`. Learn more in the [Handling collection responses](../../advanced_topics/collections/) section.

=== "Request"

    ```HTTP hl_lines="1"
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
        "holdings":{
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

## Change ("patch") fields

Given the right permissions you can update any object by issuing a `PATCH` request. 

Apart from the `manager` and `money_type` fields all portfolio fields can be updated. The manager field defines the type of portfolio management: self directed, under an advisory mandate, under a discretionary mandate. Once that is set it is not supposed to change. Same for the money_type. That field states if the portfolio manages real money or paper money. It’s obvious that this field cannot be switched at will.

Objects in the InvestSuite system are immutable. Every change leads to a new version so that a log exists of who performed which change at which moment. The version number is returned alongside other metadata fields. Use the Admin Console to access this log and to view diffs between versions.

=== "Request"

    ```HTTP hl_lines="1"
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
        "holdings":{
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
        "holdings":{
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

## Block a portfolio

Both for Self Investor portfolios as well as for Robo Advisor portfolios access can be blocked. In the case of Self Investor this means the customer can no longer trade, the account is blocked as it were. For Robo Advisor when the portfolio is blocked, the Robo Advisor will no longer compute optimisations to provide rebalancing recommendations.

=== "HTTP"

    ```HTTP hl_lines="1"
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

Manager | Description | Data type | Example | Required
------- | ----------- | --------- | ------- | --------
Self Investor | The active field for Self Investor is used to block users from placing orders, and potentially of access to any other features. | `boolean (default: false)` | false | no
Robo Advisor | Whether the optimizer service is active for this portfolio. When this field is set to true, the Robo Advisor will compute optimizations once a day and provide rebalancing recommendations. | `boolean (default: false)` | false | no