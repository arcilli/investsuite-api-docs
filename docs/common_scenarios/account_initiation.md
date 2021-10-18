---
title: Account initiation
---

!!! Info
    Applicable to: Robo Advisor, Self Investor, StoryTeller

## Context

To intiate an account two steps are required: create a user and create a portfolio. To create a user, register the user ID identifying the customer in your system. The InvestSuite API will then create a user in the system, storing this user ID and creating an InvestSuite ID that uniquely identifies the user in our system. Then use the InvestSuite ID that the API returns to create a portfolio with this user as owner.

## Create a user

User objects serve two purposes. Firstly to grant access to one or more InvestSuite products i.e. StoryTeller, Robo Advisor, Self Investor or the Admin Console. Secondly to assign users as owners to one or more portfolios. To create a user a limited number of fields are required, i.e. a user ID and optionally the user's name. Later on, more data can be stored in the user objects, depending on the InvestSuite products that your customer will be using.

**Basic fields**

You can add the ID of that user in your system in the `external_id` field, also called the reference ID.

Next to your user ID you can optionally register the first name and last name. Let’s now create a user and then see why adding the user’s first name and last name may be a good idea.

=== "HTTP"

    ```HTTP 
    POST /users/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Content-Type: application/json
    Authorization: Bearer {string}

    {
        "external_id": "unique_external_entity_id",
        "first_name": "Ashok",
        "last_name": "Kumar"
    }

    ```

=== "curl"

    ```bash
    curl -X POST \                 
    -H "Content-Type: application/json" \
    -H "Auhorization": "{string}"  \   
    -d '{  \   
            "external_id": "unique_external_entity_id",  \   
            "first_name": "Ashok", \
            "last_name": "Kumar", \
        }'
    https://api.sandbox.investsuite.com/users/
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`external_id` | A unique external identifier for this entity, also referred to as Reference ID. This identifier can be any string used in your system to identify this entity. | `string <= 64 characters` | unique_external_entity_id | no 
`first_name` | The first name of the user. | `string <= 128 characters` | Ashok | no
`last_name` | The last name of the user. | `string <= 128 characters` | Kumar | no

**Response body** 

```JSON
{
    "external_id": "unique_external_entity_id",
    "id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
    "creation_datetime": "2021-02-18T08:21:02+00:00",
    "version": 1,
    "version_datetime": "2021-02-18T08:21:02+00:00",
    "version_authored_by_user_id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
    "deleted": false,
    "first_name": "Ashok",
    "last_name": "Kumar",
}
```

Now that you have successfully registered the user you can find that user in the InvestSuite Admin Console. Contact your representative to get credentials to access the Admin Console. Having provided the user’s name now comes in handy.

![InvestSuite Admin Console](../img/admin_console.png)

What else:

* You can create [logins for your customers](#create-login) for them to view and manage their portfolios. (Next)
* You can [add the counter account](#add-a-counter-account) for withdrawal instructions.
* You can [change (“patch”) fields](../users/#change-patch-fields).
* You can [delete the user](../users/#delete-a-user).
* You can [search for users](../users/#search) by Reference ID, the user’s name etc.
* You can [create a portfolio](#create-portfolio) for the user.

## Create login

InvestSuite prefers not to store personal data (PII) in the InvestSuite platform. Instead we merely demand an identifier to reference your client. However if you want to create credentials on the InvestSuite platform so that your user can log in to the platform, or in fact to the front-ends exposing the platform, you need to register the user’s e-mail address and mobile number.

=== "HTTP"

    ```HTTP hl_lines="1"
    POST /users/?create_idp_user=true HTTP/1.1
    Host: api.sandbox.investsuite.com
    Content-Type: application/json
    Authorization: Bearer {string}

    {
        "external_id": "unique_external_entity_id",
        "first_name": "Ashok",
        "last_name": "Kumar",
        "email": "ashok.kumar@example.com",
        "phone": "+12345667"
    }

    ```

=== "curl"

    ```bash
    curl -X POST \                 
    -H "Content-Type: application/json" \
    -H "Auhorization": "{string}"  \   
    -d '{  \   
            "external_id": "unique_external_entity_id",  \   
            "first_name": "Ashok", \
            "last_name": "Kumar", \
            "email": "ashok.kumar@example.com", \
            "phone": "+123456789"
        }'
    https://api.sandbox.investsuite.com/users/?create_idp_user=true
    ```

By passing the e-mail address and phone number of your client you make InvestSuite register the user in the identify platform (IdP) linked to your configuration - which in most cases is Azure ADB2C. Since e-mail and phone number are required to identify your customer in the identify platform both fields are required to be unique. On top, these fields are _write once_. You cannot update them.

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`email` | The e-mail address of the user. Unique and write once. | `email` | ashok.kumar@example.com | no 
`phone` | The mobile phone number of the user in international format. Unique and write once. | `string (^\+[1-9][0-9]{7,15}\Z)` | +123456789 | no

When the user is registered in the IdP for a customer, that customer can then go through a process to obtain a password. This process takes place outside of InvestSuite and the bank and requires the user to access the identity platform immediately.

Now, with the e-mail address and the password the user can authenticate against the IdP. When authentication is successful the IdP returns a JWT. You can then add the InvestSuite User ID in the JWT claims to interact with the API on behalf of that user, for instance going through risk profiling on the person’s behalf, retrieving portfolio data, issuing buy or sell orders etc.

![IdP integration](../img/idp-integration.jpg)

## Add a counter account

You can optionally add a counter account during user creation; or afterwards by patching the user object. This is used to display to your customer the account that is funded in case of a withdrawal instruction. In some cases banks allow the customer to modify the counter account via its front-end.

=== "HTTP"

    ```HTTP 
    POST /users/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Content-Type: application/json
    Authorization: Bearer {string}

    {
        "external_id": "unique_external_entity_id",
        "first_name": "Ashok",
        "last_name": "Kumar",
        "email": "ashok.kumar@example.com",
        "phone": "+12345667",
        "counter_account": {
            bank_account_number: "BE01234567891234",
            bank_account_type: "IBAN",
            bank_id: "IDQMIE2D"
        }
    }

    ```

=== "curl"

    ```bash
    curl -X POST \                 
    -H "Content-Type: application/json" \
    -H "Auhorization": "{string}"  \   
    -d '{  \   
            "external_id": "unique_external_entity_id",  \   
            "first_name": "Ashok", \
            "last_name": "Kumar", \
            "email": "ashok.kumar@example.com", \
            "phone": "+123456789", \
            "counter_account": { \
                bank_account_number: "BE01234567891234", \
                bank_account_type: "IBAN", \
                bank_id: "IDQMIE2D" \
            }
        }'
    https://api.sandbox.investsuite.com/users/
    ```

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`bank_account_number` | Account number of the user for withdrawal instructions. | `string ^[A-Z]{2}[A-Z0-9]{14,30}\Z` | BE01234567891234 | yes
`bank_account_type` | Type of the bank account number that is associated with this portfolio, typically an IBAN number. | `enum("ABA", "IBAN")` | IBAN | yes
`bank_id` | Bank identifier code or ID of the bank used for routing instructions, typically a BIC identifier. | `string AnyOf("^[0-9]{9}\Z", "^[A-Z]{4}[A-Z]{2}[A-Z0-9]{2}([A-Z0-9]{3})?\Z")` | IDQMIE2D | no

## Create a portfolio

With a InvestSuite User ID at hand you can create a portfolio for your customer. Let’s first do that and then go over it line by line.

=== "HTTP"

    ```HTTP hl_lines="11"
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
            "manager_version":1
            "manager_settings": {
                "policy_id":"Y01EF46X9XB437JS4678X0K529C",
            }
        },
        "external_id":"your-bank-portfolio-1",
        "money_type":"REAL_MONEY",
        "owned_by_user_id":"U01F5WYKRRXZHXT9S6FF1JZNJVZ",
    }

    ```

=== "curl"

    ```bash
    curl -X POST \                 
    -H "Content-Type: application/json" \
    -H "Auhorization": "{string}"  \   
    -d '{  \   
        "base_currency":"USD",  \   
        "config":{  \   
            "manager":"ROBO_ADVISOR_DISCRETIONARY",  \   
            "manager_version":1  \   
            "manager_settings": {  \   
                "policy_id":"Y01EF46X9XB437JS4678X0K529C",  \   
            }  \   
        },  \   
        "external_id":"your-bank-portfolio-1",  \   
        "money_type":"PAPER_MONEY",  \   
        "owned_by_user_id":"U01F5WYKRRXZHXT9S6FF1JZNJVZ",  \   
    }' \
    https://api.sandbox.investsuite.com/portfolios/
    ```

**Response body**

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

**What is a portfolio to InvestSuite** - An investment portfolio in the classical sense of the term is a collection of financial assets grouped with the aim of earning a profit. These assets are referred to as holdings. Alongside holdings, portfolio objects in the InvestSuite platform contain general info, for instance to display to the customer, and configuration settings to manage the portfolio. An example of general info is a portfolio name e.g. My Retirement Portfolio, examples of configuration settings are the active boolean to indicate that trading is allowed for a portfolio and the risk profile the customer had set for the portfolio.

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`name` | The display name of the portfolio set by the user. | `string <= 128 characters` | My Portfolio-1 | yes
`owned_by_user_id` | The portfolio owner's user ID. This field is required firstly to get to the account of the owner for cash withdrawals, and secondly to group portfolios by owner. | `string ^U[0-9A-HJKMNP-TV-Z]{26}\Z` | U01ARZ3NDEKTSV4RRFFQ69G5FAV | yes
`base_currency` | The portfolio's currency. | `string ^[A-Z]{3}\Z` | USD | yes
`money_type` | Defines whether this is a 'virtual' portfolio or not. In a virtual portfolio buying and selling decisions are simulated, rather than placed as actual orders through a broker. ! Write once | `enum("REAL_MONEY", "PAPER_MONEY")` | REAL_MONEY | yes
`config->manager` | The manager type is either: self managed, or managed by the Robo Advisor under an advisory or discretionary mandate. For **Self Investor** select `USER_MANAGED` ! Write once. | `enum("USER_MANAGED", "ROBO_ADVISOR_ADVISORY", "ROBO_ADVISOR_DISCRETIONARY")` | ROBO_ADVISOR_DISCRETIONARY | yes
`config->manager_version` | Which major version of the selected portfolio manager to use. | `integer >= 1` | 1 | yes
`brokerage_account` | Account information associated with this portfolio | `Object` |  | no
`brokerage_account->bank_account_number` | Account number of the portfolio owner's bank account associated with this portfolio for payment instructions. | `string ^[A-Z]{2}[A-Z0-9]{14,30}\Z` | BE01234567891234 | yes
`brokerage_account->bank_account_type` | Type of the bank account number that is associated with this portfolio, typically an IBAN number. | `enum("ABA", "IBAN")` | IBAN | yes
`brokerage_account->bank_id` | Bank identifier code or ID of the bank used for routing instructions, typically a BIC identifier. | `string AnyOf("^[0-9]{9}\Z", "^[A-Z]{4}[A-Z]{2}[A-Z0-9]{2}([A-Z0-9]{3})?\Z")` | IDQMIE2D | no

## Portfolio management settings

For the Robo Advisor to operate, there is a limited number of management settings that need configuration, e.g. the related investment policy or the user’s risk profile. Either while creating the portfolio issuing a POST request, or afterwards by issuing a PATCH request you can register these management settings.

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`policy_id` | The ID of the policy applied in the optimization of this portfolio. Investment Policies reflect a financial institution’s investment strategies for specific audiences, or even individuals. To get a list of investment policies issue `GET /robo-advisor/policies/`. | Y01ARZ3NDEKTSV4RRFFQ69G5FAV | `string ^Y[0-9A-HJKMNP-TV-Z]{26}\Z` | no 
`risk_profile_id`| The ID of the risk profile associated with this portfolio, optionally set by a customer. In case of Robo Advisor, a policy will be proposed for this Portfolio based on the computed risk profile score. | `string ^K[0-9A-HJKMNP-TV-Z]{26}\Z` | K01ARZ3NDEKTSV4RRFFQ69G5FAV | no
`goal_id`  | (Robo Advisor only) The ID of the goal associated with this portfolio. To get a list of investment policies issue `GET /robo-advisor/goals/`. | `string ^L[0-9A-HJKMNP-TV-Z]{26}\Z` | L01ARZ3NDEKTSV4RRFFQ69G5FAV | no
`horizon_id` | (Robo Advisor only) The ID of the horizon associated with this portfolio. To get a list of investment policies issue `GET /robo-advisor/horizons/`. | `string ^H[0-9A-HJKMNP-TV-Z]{26}\Z` | H01ARZ3NDEKTSV4RRFFQ69G5FAV | no

What else:

* You can [block a portfolio](../portfolios/#block-a-portfolio) 
* You can [change (“patch”) portfolio fields](../portfolios/#change-patch-fields)
* You can [delete the portfolio](../portfolios/#delete-a-portfolio)
* You can [search for portfolios](../portfolios/#delete-a-portfolio)
