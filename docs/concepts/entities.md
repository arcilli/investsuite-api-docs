---
title: Business Objects
---

## Introduction

This page describes the business objects used by InvestSuite, and the interactions that are possible on them.

The business objects are also referred to as (a collection of) entities.

### Concepts

**Internal ID**

The first letter of the internal ID of an entity is defined by its type, eg. the ID of a Portfolio will always start with a `P`.

**External ID**

!!! info "Middleware"

    The `external_id` typically maps to the id in the Client middleware.

Entities have an `external_id` field. The field is not required, but if it is provided, it must be unique.



**Immutability**

Objects in the InvestSuite system are immutable. Every change leads to a new version so that a log exists of who performed which change at which moment. The version number is returned alongside other metadata fields. Use the Admin Console to access this log and to view diffs between versions.
## Querying business objects

To query a specific business object, send a `GET` request to the entity root path e.g. `GET /users`, `GET /portfolios`, `GET /instrument_groups`.

Collection endpoints accept the following parameters:

Parameter | Description
--------- | -----------
limit     | Example: `limit=50`. Allows you to pass in the number of items to be returned in the results array of the response. The default collection response size is `20 items`. The maximum size is 100.
offset    |  Example `offset=50`. To be used in combination with the `limit` parameter, the offset defines the number of records that need to be skipped.
embed     | Example: `embed=field_name_1,field_name_2`. Optional comma-separated list of field names for which the referenced entity is to be included in the _embedded object of the response. See [Embedding](embedding.md).
query    | Example: `query=email+eq+'jane.doe@example.com`. See below.

=== "Request"

    ```HTTP hl_lines="1 2 3"
    GET /portfolios/
        ?embed=owned_by_user_id
        &limit=2 HTTP/1.1
    Host: api.sandbox.investsuite.com
    Content-Type: application/json
    Authorization: Bearer {string}
    ```

=== "Response (body)"

    ```JSON hl_lines="10"
    {
        "next":"http://api.sandbox.investsuite.com/portfolios/?embed=owned_by_user_id&limit=2",
        "count":204,
        "offset":"10",
        "results":[
        {
            "external_id":"AE820230000000000001918",
            "readable_by":[
                "U01ERYVVSZTKKYEEVX3Y6JECE83"
            ],
            "modifiable_by":[
                "U01ERYVVSZTKKYEEVX3Y6JECE83"
            ],
            "name":"Child's education",
            "owned_by_user_id":"U01ERYVVSZTKKYEEVX3Y6JECE83",
            "base_currency":"USD",
            "money_type":"REAL_MONEY",
            "config":{
                "manager":"ROBO_ADVISOR_DISCRETIONARY",
                "manager_version":1,
                "manager_settings":{
                    "policy_id":"Y01F5G34T4FE6T1EH798FA0M170",
                    "goal_id":"L01EF46X3JH1QDFPD270BKSDTTX",
                    "horizon_id":"H01EF46X564YMWFR8GTNBED16CN",
                    "risk_profile_id":"K01EF46X76V5VB4E92FC1D01856",
                    "divest_amount":null,
                    "start_amount":600.0,
                    "recurring_deposit_amount":500.0,
                    "end_datetime":null,
                    "active":true,
                    "proposed_risk_profile_id":"K01EF46X76V5VB4E92FC1D01856",
                    "tags":[
                    
                    ],
                    "risk_profile_score":3,
                    "background_image":"https://images.unsplash.com/photo-1526304640581-d334cdbbf45e?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1200&q=82",
                    "background_color":null
                }
            },
            "brokerage_account":{
                "brokerage_portfolio_id":"BrokerAccountKey",
                "brokerage_user_id":"BrokerAccountId",
                "bank_account_type":"IBAN",
                "bank_account_number":"AE820230000000000001918",
                "bank_id":null
            },
            "owned_by_user_ids":[
                
            ],
            "portfolio":{
                
            },
            "snapshot_datetime":null,
            "archived":false,
            "funded_since":null,
            "id":"P01F7KDVY6H718JNV91840DE5V4",
            "creation_datetime":"2021-06-07T00:00:00+00:00",
            "version":2,
            "version_datetime":"2021-06-07T14:26:21.231855+00:00",
            "version_authored_by_user_id":"U01ERYVVSZTKKYEEVX3Y6JECE83",
            "deleted":false,
            "_embedded":{
                
            },
            "status":"WAITING_FOR_FUNDS"
        }
        ],
        "_embedded":{
        "U01ERYVVSZTKKYEEVX3Y6JECE83":{
            "external_id":null,
            "readable_by":[
                "U01F5XFY0S38KJYVHX0X3GJ4KWM",
                "UXXXXXXXXXXXXXXXXXXXXXXXXXX"
            ],
            "modifiable_by":[
                "U01F5XFY0S38KJYVHX0X3GJ4KWM",
                "UXXXXXXXXXXXXXXXXXXXXXXXXXX"
            ],
            "id":"U01F5XFY0S38KJYVHX0X3GJ4KWM",
            "creation_datetime":"2021-05-17T00:00:00+00:00",
            "version":1,
            "version_datetime":"2021-05-17T15:43:23.427412+00:00",
            "version_authored_by_user_id":"UXXXXXXXXXXXXXXXXXXXXXXXXXX",
            "deleted":false,
            "_embedded":{
                
            },
            "first_name":"Laurent",
            "last_name":"Sorber",
            "email":"laurent.sorber@gmail.com",
            "phone":null,
            "brokerage_user_id":null,
            "idp_user_id":"a8c8b808-bad9-4acb-babf-aae52488e468",
            "language":"en-US",
            "products":[
                
            ]
        }
        }
    }    
    ```

### Query syntax

The API provides a structural search and filtering mechanism for all entities. We already opted to work with a query string parameter `query=email+eq+'jane.doe@example.com` instead of a POST body for more efficient client-side caching and ease of use for testing. The query syntax is a mix of both [OData](https://www.odata.org/) and [Apache Lucene Query Parser Syntax](https://lucene.apache.org/core/2_9_4/queryparsersyntax.html).

=== "Request"

    ```HTTP hl_lines="2"
    GET /portfolios
        ?query=lastmodified+in+['20200101'+to+'20240101'] HTTP/1.1
    Host: api.sandbox.investsuite.com
    Content-Type: application/json
    Authorization: Bearer {string}
    ```

!!! info

    - Every entity type can be queried by issuing a `GET` request e.g. `GET users/?query=email+eq+'jane*'`.
    - The query string must be URL encoded. This implies that spaces would be transformed to `%20`, but for improved legibility we recommend using `+` instead

### Operators

!!! warning

    Due to implementation details (eg. amounts are typically stored as strings to preserve precision) the behavior of these operators may vary.

Operator type | Operator  | Example
--- | --- | ---
Number operator  | Exact match | `age eq 1`
Number operator  | Less than | `age lt 18`
Number operator  | Less than or equal | `age le 18`
Number operator  | Greater than | `age gt 18`
Number operator  | Greater than or equal | `age ge 18`
String operator  | Exact match | `email eq 'jane.doe@example.com'`
String operator  | Prefix search | `email eq john*`
String operator  | Postfix search | `email eq '*gmail.com'`
String operator  | `LIKE` matching | `email eq '*gmail*'`
Date operator    | Exact match | `last_modified eq '2020-09-11T09:03:53.721588+00:00'`
Date operator    | Is in range for dates without time | `lastmodified in ['20200101' to '20210101']`
Date operator    | Is in range for dates with time | `lastmodified in ['2020-09-11T09:03:53.721588' to '2020-11-11T09:03:53.721588']`
Date operator    | Is in range for dates with timeezone | `lastmodified in ['2020-09-11T09:03:53.721588+00:00' to '2020-11-11T09:03:53.721588+00:00']`
Boolean operator | Boolean queries | `archived eq true`
Null comparison  | Equals `null` | `field eq null`<br>NOTE the `is null` operator is (by design) not supported.
Null comparison  | Not equal to `null` | `field neq null`
List operator    | `IN` (element of) | `field in ['value1', 'value2', 'value3']`
Logical operator | `AND`, `OR` | `email eq kristof and age gt 18`

!!! Hint
    You can combine operators
    in all possible compositions e.g. `query=email+eq+'*tom*'+and+firstname+in+[’tom',+‘thomas’]`.
### Sorting

The sorting operator always comes as the last term, except when there is a selection term which is always at the very last:

- orderby {attribute_name}, orderby {attribute_name} asc e.g. `orderby+age` is always ascending, so the same as `orderby+age+asc`
- Order descending: orderby {attribute_name} desc e.g. `orderby+age+desc`
- Multiple attributes: orderby {attribute_name} desc, {attribute_name2} asc e.g. `orderby+age+desc,+firstname`
- Attributes names can be nested e.g. `orderby+manager.bank_account`.

## Pagination

Business objects can be enumerated by paginating through the entire collection.

Issuing a `GET` request to the entity root path will return a next cursor:

=== "Request"

    ```HTTP
    GET /portfolios HTTP/1.1
    Host: api.sandbox.investsuite.com
    Content-Type: application/json
    Authorization: Bearer {string}
    ```

=== "Response"

    ```JSON hl_lines="2"
    {
        "next": "/portfolios/?limit=20&offset=20",
        "count": "20",
        "offset": "20",
        "results": [
            ...
        ],
        "_embedded": {}
    }
    ```

=== "Response (End)"

    ```JSON hl_lines="2"
    {
        "next": null,
        "count": "15",
        "offset": "20",
        "results": [
            ...
        ],
        "_embedded": {}
    }
    ```

## List of business objects

Common:

- [Users](users.md)
- [Portfolio](portfolios.md)
- [Transaction](transactions.md)

Robo Advisor only:

- [Optimization](../robo/optimization.md)




<!-- A Transaction contains the latest version of every Movement (determined by its status), but there are two distinct status groups that determine what is the latest status: 

1. PLANNED -> PENDING -> PLACED
2. CANCELLED / NOT_EXECUTED / EXPIRED / [EXECUTED -> SETTLED]. 

In a Transaction, you can have a latest Movement in status group (1) and one or more corresponding latest Movements in status group (2). -->

<!-- https://investsuite.slack.com/archives/CDYGVNQKE/p1661327434299829?thread_ts=1661250223.596519&cid=CDYGVNQKE -->




<!-- TODO
    # A: Risk Profile Question Answer (deprecated)
    # B: Suitability Profiler User Property Assignment
    # C: Suitability Profiler Cooldown
    # D: Suitability Profiler User Property
    # E: Suitability Profiler Question
    # F: Portfolio Group
    # G: UserGroup
    # H: Horizon
    # I: Instrument
    # J: InstrumentGroup
    # K: Risk Profile
    # L: Goal
    # M: Suitability Profiler Questionnaire
    # N: Suitability Profiler Profile
    # O: Optimization
    # P: Portfolio
    # Q: Risk Profile Question (deprecated)
    # R: Risk Profile Question Response (previously: Role, deprecated)
    # S: Portfolio Report
    # T: Transaction
    # U: User
    # V: Portfolio Valuation
    # W: Suitability Profiler Profile roperty
    # X: Suitability Profiler Profiler
    # Y: Policy
    # Z: Agreement
 -->
