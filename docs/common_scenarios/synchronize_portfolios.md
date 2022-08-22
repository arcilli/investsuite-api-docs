---
title: Synchronize portfolios
---

## Context

Synchronizing a portfolio involves taking the holdings and the transactions for a portfolio from your brokerage and custody system and passing them to InvestSuite. As an API client you are responsible for keeping the holdings and transactions in sync between the broker / custody system and the InvestSuite platform. The master data source for both the positions and the transactions is that of the broker / custody provider. For Robo Advisor you copy the holdings and transactions into the InvestSuite system on a recurring bases, for instance once a day. For Self Investor this synchronization should run in near real time.

### Transactions definition

Transactions are a combination of movements that describe an exchange or trade. In the context of investing these transactions relate to funds and securities transferred into, and from an account. A bank or other licensed entity engages in transactions on behalf of invididuals or organizations. The  bank therefore uses some sort of an exchange. Each transaction is recorded in a ledger.

Transactions can be investment transactions as well as cash transactions. Investment transactions relate to the purchase and sale of securities. Cash transactions mainly describe a deposit of cash in the portfolio, or a divestment. Other forms of cash transactions are those with respect to fees and taxes. A third important of set of transactions consider corporate action such as for instance a stock split or distribution of dividends.

### Holdings definition

Holdings are positions in a portfolio. Holdings are the assets out of which a portfolo is composed. Holdings can be either investment products or cash holdings. Cash holdings are expressed in their currency.

Example:
```JSON
{
    "$EUR": 12000,
    "BE92189F1066":127,
    "NL3160923039":57,
    "FR3160928731":398
}
```

## Send transaction

Transactions are synchronized to calculate the performance and return of a portfolio, to report to the customer, and to monitor the status of some transactions for instance to exchange if a `BUY` transaction is _pending_, _executed_ or _settled_.

For Robo Advisor it is usually sufficient to synchronize transactions on a daily bases. For Self Investor synchronization it is required to notify InvestSuite in realtime. To facilitate this, as an alternative to using the InvestSuite API REST interfaces you can send commands to a (Kafka) message broker. Reach out if you wish to discuss this setup.

The specification for the Transaction entity is more extensive than described below. For the full specification please refer to the [API specifications documentation](https://api.sandbox.investsuite.com/redoc).

Field | Description | Data type | Example | Required
----- | ----------- | --------- | ------- | --------
`external_id` | A unique external identifier for this entity, also referred to as Reference ID. This identifier can be any string used in the client's system to identify this entity. This field is not checked for uniqueness. | `string <= 64 characters` | your-object-id | no
`order_type` | Defines for orders if it is a market or a limit order. | `Enum("MARKET","LIMIT")` | MARKET | no
`movements` | A list of movements that constitute this transaction. A transaction always consists of at least 1 movement. Typical movements include: cash deposit or withdrawal (1 cash movement), equity order purchase (1 cash movement, 1 share movement, and optionally a third movement for execution costs), period fee payment (1 cash movement). | `object` |  |  yes
`movements->type` | Type of the movement. This enumerator includes all supported movement types that can occur in a transaction. | `Enum("CASH_DEPOSIT", "CASH_DIVIDEND", "CASH_WITHDRAWAL", "COUPON", "BUY", "SELL", "CORPORATE_ACTION_IN", "CORPORATE_ACTION_OUT", "REVERSE_STOCK_SPLIT", "STOCK_DIVIDEND", "STOCK_SPLIT", "TRANSFER_IN", "TRANSFER_OUT", "CUSTODY_FEE", "INSTRUMENT_ENTRY_EXIT_FEE", "MANAGEMENT_FEE", "OTHER_FEE", "OTHER_TAX", "SERVICE_ENTRY_EXIT_FEE", "TRANSACTION_FEE", "WITHHOLDING_TAX")` | BUY | yes
`movements->trade_type` | Indicate if a movement is cancelled or rebook, particularly applicable to cash movements for dividends. | `enum("CANCEL", "REBOOK", "INSTRUMENT_CHANGE", "CORRECTION")` | REBOOK | no
`movements->status` | The status of the movement. Movement are in final state e.g. EXECUTED, or in progress e.g. PLANNED, SETTLED, NOT_EXECUTED... | `Enum("PLANNED", "PENDING", "PLACED", "EXECUTED", "SETTLED", "NOT_EXECUTED", "EXPIRED", "CANCELLED")` | SETTLED | yes
`movements->datetime` | The date and time on which the movement was registered. | `date-time` | 2025-06-04T15:23:15.328252+00:00 | yes
`movements->instrument_id` | Identifier of the instrument involved in this movement. ‘$’ prefix in case of a cash movement where the instrumentId is a currency. ISIN in case of a securities movement where the instrumentId refers to an actual instrument. | `String ^(\$[A-Z]{3}|[A-Z]{2}[A-Z0-9]{9}[0-9]|[A-Z]{1` | LU4642865251 | yes
`movements->quantity` |  Quantity of the movement expressed in units of the instrument. If the instrument_id is a cash type such as $USD, the quantity indicates a cash amount. If the instrument_id is an investible product such as IE0031442068, the quantity indicates a share amount. Fractional amounts are supported for instruments such as ETFs.Quantity is positive when adding a quantity of an instrument to the portfolio and negative when removing a quantity of an instrument from a portfolio.	| `number` | 5 | yes
`movements->unit_price` | The instrument's price per unit expressed in the instrument's currency in case the instrument is a security. For the cash movement of a cash dividend to store the dividend value. | `number` | 1000 | no
`movement->description` | An optional description for this movement. This string can contain any additional details of the movement type and can be set by the client. | `string` | STAMP_DUTY | no
`movement->reference_instrument_id` | Optional instrument ID of the portfolio position that this movement relates to. For example, if this Movement represents a cash dividend, this field may refer to the instrument in the portfolio that generated that dividend.| `string` | LU4642865251 | no

Some common transactions to pass to InvestSuite are:

1. Buy transactions
2. Sell transactions
3. Cash movements
4. Costs and charges
5. Dividend payments

### 1. Buy transactions

A buy transaction describes the properties of a buy order. It consists of three movements:
- one describing the instrument to be purchased at the time of placement,
- a second describing the instrument purchased at the time of settlemet,
- a third the amount that was transferred from the cash account to buy the instrument.

=== "Request"

    ```HTTP
    POST /portfolios/P01FGVEKTV86PPKQVRK9CHT31JR/transactions/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Content-Type: application/json
    Authorization: Bearer {string}

    {
        "external_id": "your-transaction-id-1",
        "movements": [
            {
                "type": "BUY",
                "status": "PLACED",
                "datetime": "2022-06-10T07:49:26.341Z",
                "instrument_id": "LU78468R1014",
                "quantity": 1
            },
            {
                "type": "BUY",
                "status": "SETTLED",
                "datetime": "2022-06-12T07:49:26.341Z",
                "instrument_id": "LU78468R1014",
                "unit_price": 29.51,
                "quantity": 7
            },
            {
                "type": "SELL",
                "status": "SETTLED",
                "datetime": "2022-06-12T07:49:26.341Z",
                "instrument_id": "$USD",
                "quantity": -206.57
            }
        ]
    }
    ```

=== "Response (body)"

    ```JSON
    {
        "external_id": "your-transaction-id-1",
        "movements": [
            {
                "type": "BUY",
                "status": "PLACED",
                "datetime": "2022-06-10T07:49:26.341Z",
                "instrument_id": "LU78468R1014",
                "quantity": 1
            },
            {
                "type": "BUY",
                "status": "SETTLED",
                "datetime": "2022-06-12T07:49:26.341Z",
                "instrument_id": "LU78468R1014",
                "unit_price": 29.51,
                "quantity": 7
            },
            {
                "type": "SELL",
                "status": "SETTLED",
                "datetime": "2022-06-12T07:49:26.341Z",
                "instrument_id": "$USD",
                "quantity": -206.57
            }
        ],
        "id": "T01FHCP1CZ9F1S207KJHNA5V244",
        "creation_datetime": "2021-10-07T06:11:22.217585+00:00",
        "version": 2,
        "version_datetime": "2021-10-08T06:15:51.106890+00:00",
        "version_authored_by_user_id": "UXXXXXXXXXXXXXXXXXXXXXXXXXX",
        "deleted": false
    }
    ```    

### 2. Sell transactions

A sell transaction describes the properties a sell order. It is the reverse of a buy transaction It consists of three movements:
- one describing the instrument to be disposed as planned,
- a second when the sell instruction was settled,
- the third the amount to be transferred when the instrument is traded.

=== "Request"

    ```HTTP
    POST /portfolios/P01FGZK41MJ4NJXKZ27VJC0HGS9/transactions/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Content-Type: application/json
    Authorization: Bearer {string}

    {
        "external_id": "your-transaction-id-1",
        "movements": [
            {
                "type": "SELL",
                "status": "PLACED",
                "datetime": "2022-06-10T07:49:26.341Z",
                "instrument_id": "LU78468R1014",
                "quantity": -7
            },
            {
                "type": "SELL",
                "status": "SETTLED",
                "datetime": "2022-06-12T07:49:26.341Z",
                "instrument_id": "LU78468R1014",
                "unit_price": 29.51,
                "quantity": -7
            },
            {
                "type": "BUY",
                "status": "SETTLED",
                "datetime": "2022-06-12T07:49:26.341Z",
                "instrument_id": "$USD",
                "quantity": 206.57
            }
        ]
    }
    ```

=== "Response (body)"

    ```JSON
    {
        "external_id": "your-transaction-id-1",
        "movements": [
            {
                "type": "SELL",
                "status": "PLACED",
                "datetime": "2022-06-10T07:49:26.341Z",
                "instrument_id": "LU78468R1014",
                "quantity": -7
            },
            {
                "type": "SELL",
                "status": "SETTLED",
                "datetime": "2022-06-12T07:49:26.341Z",
                "instrument_id": "LU78468R1014",
                "unit_price": 29.51,
                "quantity": -7
            },
            {
                "type": "BUY",
                "status": "SETTLED",
                "datetime": "2022-06-12T07:49:26.341Z",
                "instrument_id": "$USD",
                "quantity": 206.57
            }
        ],
        "id": "T01FHCP1CZ9F1S207KJHNA5V244",
        "creation_datetime": "2021-10-07T06:11:22.217585+00:00",
        "version": 2,
        "version_datetime": "2021-10-08T06:15:51.106890+00:00",
        "version_authored_by_user_id": "UXXXXXXXXXXXXXXXXXXXXXXXXXX",
        "deleted": false
    }
    ```    

### 3. Cash movements

Transactions that hold cash movements respresent to InvestSuite movements on the investment account. That account is usually different from the current account, which is the account that the client holds with the bank. We expect in other words transactions on your brokerage system, not from your core banking platform.

Below is an example for a cash deposit. For a cash withdrawal use `"type": "CASH_WITHDRAWAL"` and `"quantity": -500`.

=== "Request"

    ```HTTP
    POST /portfolios/P01FGZK41MJ4NJXKZ27VJC0HGS9/transactions/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Content-Type: application/json
    Authorization: Bearer {string}

    {
        "external_id": "P01FHAR57WS6Q8AV1GH5EATYKP1/14031738752",
        "movements": [
            {
                "type": "CASH_DEPOSIT",
                "status": "SETTLED",
                "datetime": "2021-10-06T00:00:00+00:00",
                "instrument_id": "$USD",
                "quantity": 500.0,
            }
        ]
    }
    ```

=== "Response (body)"

    ```JSON
    {
        "external_id": "P01FHAR57WS6Q8AV1GH5EATYKP1/14031738752",
        "movements": [
            {
                "type": "CASH_DEPOSIT",
                "status": "SETTLED",
                "datetime": "2021-10-06T00:00:00+00:00",
                "instrument_id": "$USD",
                "quantity": 500.0,
            }
        ],
        "id": "T01FHCP1CZ9F1S207KJHNA5V244",
        "creation_datetime": "2021-10-07T06:11:22.217585+00:00",
        "version": 2,
        "version_datetime": "2021-10-08T06:15:51.106890+00:00",
        "version_authored_by_user_id": "UXXXXXXXXXXXXXXXXXXXXXXXXXX",
        "deleted": false
    }
    ```   

### 4. Costs and charges

Costs and charges come in various forms. There are items in the `type` Enum that define the sort of charge. For instance for management fees, custody fees, and transaction fees. For other types use `"type": "OTHER_FEE"` and `"description": "{string}"`. Same goes for taxes. For withholding tax paid to the governement use `"type": "WITHHOLDING_TAX"`. For other sorts of taxes charged use `"type": "OTHER_TAX"` and `"description": "{string}"`.

=== "Request"

    ```HTTP
    POST /portfolios/P01FGZK41MJ4NJXKZ27VJC0HGS9/transactions/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Content-Type: application/json
    Authorization: Bearer {string}

    {
        "external_id": "14031738752",
        "movements": [
            {
                "external_id": "14031738752",
                "type": "MANAGEMENT_FEE",
                "status": "SETTLED",
                "datetime": "2021-10-03T08:00:16.733954+00:00",
                "instrument_id": "$USD",
                "quantity": -0.1035719,
            }
        ]
    }
    ```

=== "Response (body)"

    ```JSON
    {
        "external_id": "14031738752",
        "movements": [
            {
                "type": "MANAGEMENT_FEE",
                "status": "SETTLED",
                "datetime": "2021-10-03T08:00:16.733954+00:00",
                "instrument_id": "$USD",
                "quantity": -0.1035719
            }
        ],
        "id": "T01FH2JNYAYQ4CTHQJ1MFDDGXZQ",
        "creation_datetime": "2021-10-03T08:00:16.734375+00:00",
        "version": 1,
        "version_datetime": "2021-10-03T08:00:16.734375+00:00",
        "version_authored_by_user_id": "UXXXXXXXXXXXXXXXXXXXXXXXXXX",
        "deleted": false
    }
    ```

Example `WITHHOLDING_TAX`:

=== "Request"

    ```HTTP
    POST /portfolios/P01FGZK41MJ4NJXKZ27VJC0HGS9/transactions/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Content-Type: application/json
    Authorization: Bearer {string}

    {
        "external_id": "14031738752",
        "movements": [
            {
                "external_id": "14031738752",
                "type": "WITHHOLDING_TAX",
                "status": "SETTLED",
                "datetime": "2021-10-03T08:00:16.733954+00:00",
                "instrument_id": "$USD",
                "quantity": -0.1035719,
            }
        ]
    }
    ```

=== "Response (body)"

    ```JSON
    {
        "external_id": "14031738752",
        "movements": [
            {
                "type": "WITHHOLDING_TAX",
                "status": "SETTLED",
                "datetime": "2021-10-03T08:00:16.733954+00:00",
                "instrument_id": "$USD",
                "quantity": -0.1035719
            }
        ],
        "id": "T01FH2JNYAYQ4CTHQJ1MFDDGXZQ",
        "creation_datetime": "2021-10-03T08:00:16.734375+00:00",
        "version": 1,
        "version_datetime": "2021-10-03T08:00:16.734375+00:00",
        "version_authored_by_user_id": "UXXXXXXXXXXXXXXXXXXXXXXXXXX",
        "deleted": false
    }
    ```

### 5. Corporate actions

Corporate actions are changes invoked by a company that affect its stakeholders in particular share and bond holders. They come in various forms and shapes: dividends, stock splits, reverse stock splits ... and are usually approved by a board of directors. Sometimes even by the shareholders who can voluntarily submit a vote.

Corporate actions are registered as transactions as they will lead to movements such as issuing dividends. Note the use of `reference_instrument_id` to reference the portfolio position the corporate action refers to.

=== "Request"

    ```HTTP
    POST /portfolios/P01FGZK41MJ4NJXKZ27VJC0HGS9/transactions/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Content-Type: application/json
    Authorization: Bearer {string}

    {
        "external_id": "your-corporate-action-1",
        "movements": [
            {
                "type": "CASH_DIVIDEND",
                "status": "SETTLED",
                "datetime": "2021-10-01T00:00:00+00:00",
                "instrument_id": "$USD",
                "quantity": 0.0785,
                "reference_instrument_id": "LU78464A6727",
            }
        ]
    }
    ```

=== "Response (body)"

    ```JSON
    {
        "external_id": "your-corporate-action-1",
        "movements": [
            {
                "type": "CASH_DIVIDEND",
                "status": "SETTLED",
                "datetime": "2021-10-01T00:00:00+00:00",
                "instrument_id": "$USD",
                "quantity": 0.0785,
                "reference_instrument_id": "LU78464A6727",
            }
        ],
        "id": "T01FGZK41MJ4NJXKZ27VJC0HGS9",
        "creation_datetime": "2021-10-02T04:10:15.570586+00:00",
        "version": 1,
        "version_datetime": "2021-10-02T04:10:15.570586+00:00",
        "version_authored_by_user_id": "UXXXXXXXXXXXXXXXXXXXXXXXXXX",
        "deleted": false
    }
    ```

## Update transaction status

After a transaction is posted in its initial state, in subsequent phases the status of the transaction changes. Most (common) transactions go from `PLANNED` to `PENDING` to `PLACED` to `EXECUTED` to `SETTLED`. In case an error occurs or a stakeholder intervenes transactions can end up in a `NOT_EXECUTED`, `EXPIRED` or `CANCELLED` state. To change the status of a transaction you issue a `PATCH` request.

=== "Request"

    ```HTTP
    PATCH /portfolios/P01FGZK41MJ4NJXKZ27VJC0HGS9/transactions/T01FGZK41MJ4NJXKZ27VJC0HGS9/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Content-Type: application/json
    Authorization: Bearer {string}

    {
        "movements": [
            {
                "type": "CASH_DIVIDEND",
                "status": "SETTLED",
                "datetime": "2021-10-01T00:00:00+00:00",
                "instrument_id": "$USD",
                "quantity": 0.0785,
                "reference_instrument_id": "LU78464A6727",
            }
        ]
    }
    ```

=== "Response (body)"

    ```JSON
    {
        "external_id": "your-corporate-action-1",
        "movements": [
            {
                "type": "CASH_DIVIDEND",
                "status": "SETTLED",
                "datetime": "2021-10-01T00:00:00+00:00",
                "instrument_id": "$USD",
                "quantity": 0.0785,
                "reference_instrument_id": "LU78464A6727",
            }
        ],
        "id": "T01FGZK41MJ4NJXKZ27VJC0HGS9",
        "creation_datetime": "2021-10-02T04:10:15.570586+00:00",
        "version": 1,
        "version_datetime": "2021-10-02T04:10:15.570586+00:00",
        "version_authored_by_user_id": "UXXXXXXXXXXXXXXXXXXXXXXXXXX",
        "deleted": false
    }
    ```

## Cancel a placed order

To turn a `PLACED` into a cancelled one, you patch the movements of the transactions with the current `PLACED` movement to a new movement with status `CANCELLED`.

Given a current status with a `PLACED` order:
```JSON
{
    "id": "T01G53YQW6E5RH7CAOBFUSCATED",
    "external_id": "my-transaction-1",
    "portfolio_id": "P01G511AYA61Z3Q14OBFUSCATED",
    "portfolio_currency": "USD",
    "type": "ORDER",
    "order_type": "LIMIT",
    "primary_movement": {
        "external_id": "my-movement-1",
        "type": "BUY",
        "status": "PLACED",
        "datetime": "2022-06-08T10:15:53.000000+00:00",
        "instrument_id": "LU2660424076",
        "quantity": "3",
        "unit_price": "3.49"
    },
    "movements": [
        {
            "external_id": "my-movement-1",
            "type": "BUY",
            "status": "PLACED",
            "datetime": "2022-06-08T10:15:53.000000+00:00",
            "instrument_id": "LU2660424076",
            "quantity": "3",
            "unit_price": "3.49"
        }
    ]
}
```

To register a cancellation you add one movement to the current list of movements. Nothing else should be updated, so the payload is as follows. Note that **you have to send the existing movements in this list as well**, because they would be lost otherwise:

=== "Request"

    ```HTTP
    POST /portfolios/P01FGZK41MJ4NJXKZ27VJC0HGS9/transactions/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Content-Type: application/json
    Authorization: Bearer {string}

    {
        "external_id": "14031738752",
        "movements": [
            {
                "external_id": "movement-1",
                "type": "BUY",
                "status": "PLACED",
                "datetime": "2022-06-08T10:15:53.000000+00:00",
                "instrument_id": "LU2660424076",
                "quantity": "3",
                "unit_price": "3.49"
            },
            {
                "external_id": "movement-2",
                "type": "BUY",
                "status": "CANCELLED",
                "datetime": "2022-06-08T11:01:54.000000+00:00",
                "instrument_id": "LU2660424076",
                "quantity": "3",
                "unit_price": "3.49"
            }
        ]
    }
    ```

=== "Response (body)"

    ```JSON
    {
        "external_id": "14031738752",
        "movements": [
            {
                "external_id": "movement-1",
                "type": "BUY",
                "status": "PLACED",
                "datetime": "2022-06-08T10:15:53.000000+00:00",
                "instrument_id": "LU2660424076",
                "quantity": "3",
                "unit_price": "3.49"
            },
            {
                "external_id": "movement-2",
                "type": "BUY",
                "status": "CANCELLED",
                "datetime": "2022-06-08T11:01:54.000000+00:00",
                "instrument_id": "LU2660424076",
                "quantity": "3",
                "unit_price": "3.49"
            }
        ],
        "id": "T01FH2JNYAYQ4CTHQJ1MFDDGXZQ",
        "creation_datetime": "2021-10-03T08:00:16.734375+00:00",
        "version": 1,
        "version_datetime": "2021-10-03T08:00:16.734375+00:00",
        "version_authored_by_user_id": "UXXXXXXXXXXXXXXXXXXXXXXXXXX",
        "deleted": false
    }
    ```

!!! Info
    Notice that the `PLACED` and `CANCELLED` movements are identical, apart from their status and their datetime. The datetime is the time the order was `PLACED` and `CANCELLED` respectively.

## Update portfolio holdings

To update the holdings you patch the portfolio.

=== "Request"

    ```HTTP
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
        "LU3160928731": 10,
        "LU97717W5215": 6,
        "LU4642861458": 4,
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
            "LU3160928731": 10,
            "LU97717W5215": 6,
            "LU4642861458": 4,
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
