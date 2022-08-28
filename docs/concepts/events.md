---
title: Events
---

Asynchronous interactions between InvestSuite and the Client Middleware can happen in multiple ways, depending on the direction. The below table summarizes the capabilities:

|  | Inbound InvestSuite | Outbound InvestSuite |
|---|---|---|
| Event Driven | *Not supported* | The Client Middleware responds to an event that is sent by the InvestSuite system |
| REST `/events/` Endpoint | The Client Middleware notifies InvestSuite of an event through the `POST /events/` endpoint | **Under development** The Client Middleware regularly polls the `GET /events/` endpoint |

## Event Driven

### Outbound

#### Architectural concept

- InvestSuite posts events on one single topic of the client's event queue
- Any AMQP/MQTT compatible message bus is supported, eg. Kafka, RabbitMQ, ...

#### Principles

1. The strategy is to signal that an event has happened, not to include all information that the middleware may possibly require (as these requirements may vary wildly and may introduce breaking changes). Therefore, an additional REST API call may be required to get additional data.

2. All events will use this envelope, with the actual information in `data`.

=== "Event body"
  ```JSON
  {
    "id": string, 
    "created": timestamp, 
    "version": string,
    "subject": string,
    "action": string, 
    "data": dict
  }
  ```

| Field | Type | Content |
|---|---|---|
| id | string | An ID uniquely identifying the event |
| created | timestamp | The timestamp of the creation of the event |
| version | string | The version of the event object. For now, this will always be "1.0.0" |
| subject | string | A string representing a subject, always referring to an entity (see [Entities Overview](entities.md)) |
| action | string | The action on the topic entity that triggers the event, for example 'creation' |
| data | dict | A dictionary that contains specific fields, depending on the type, see below for the exhaustive list. |

<!-- TODO: add link to Entities -->

!!! note
    We are deliberately using ‘subject’ i.o. ‘topic’, since 1/ the integration pattern with 3rd parties is that we will post all messages on 1 topic (so this will be confusing) and 2/ this field can, potentially in the future, be broader than only entities



#### Portfolios

##### Creation

Fully qualified name: `portfolios.creation`

When IVS supplies the the front-end application to the b2b client they should be notified by IVS when a (real money) portfolio is created. That way, the client can store the IVS portfolio_id at their side and (optionally) link it to the user in their database.

It is up to the b2b client to check if the portfolio is “paper_money” or “real_money” based on this event. This to decide if they need to proceed with setup at their side.

=== "Event body"
  ```JSON
  {
    "id" : "an-event-id",
    "created" : "-1000000000-01-01T00:00:00Z",
    "version" : "1.0.0",
    "subject" : "portfolios",
    "action" : "creation",
    "data" : {
      "id" : "a-portfolio-id",
      "external_id" : "an-external-id",
      "owned_by_user_id" : "a-user-id"
  }
  ```

##### Status update

Fully qualified name: `portfolios.status-update`

Every time a status of a portfolio gets updated an event will be sent to the b2b client.

Possible values for the status field:

- WAITING_FOR_ACCOUNT: brokerage accounts are to be added by the b2b client
- WAITING_FOR_POLICY: user is still going through the risk profiling, so a policy was not yet determined.
- WAITING_FOR_FUNDS: waiting for incoming funds to arrive. This can be a trigger for the b2b client to push the client to make a transfer if this status isn’t updated within a certain timeframe.
- ACTIVE: portfolio is in use
- INACTIVE

=== "Event body"
  ```JSON
  {
    "id" : "an-event-id",
    "created" : "-1000000000-01-01T00:00:00Z",
    "version" : "1.0.0",
    "subject" : "portfolios",
    "action" : "status-update",
    "data" : {
      "id" : "a-portfolio-id",
      "external_id" : "an-external-id",
      "value" : "INACTIVE"
    }
  }
  ```

Possible values for the value field:

- WAITING_FOR_POLICY
- WAITING_FOR_ACCOUNT
- WAITING_FOR_FUNDS
- ACTIVE
- INACTIVE

##### Funding

Fully qualified name: `portfolios.funding`

=== "Data body"
  ```JSON
  {
    "id": "P01F8ZSNV0J45R9DFZ3D7D8C26F",
    "value": 500, 
    "currency": "EUR"
  }
  ```

##### Withdrawal

Fully qualified name: `portfolios.withdrawal`

Whenever a user wants to withdraw money, IVS will update the divest amount on the portfolio object.

Every update to this amount will trigger an event towards the b2b client.

Divest amount:

- 1e100 → full withdrawal.
- a specific amount → partial withdrawal

Note: value is the total amount that is requested for withdrawal, not the incremental value (eg. in case of two quickly subsequent withdrawal requests, where the first is not yet settled)

=== "Event body"
  ```JSON
  {
    "id" : "an-event-id",
    "created" : "-1000000000-01-01T00:00:00Z",
    "version" : "1.0.0",
    "subject" : "portfolios",
    "action" : "divest-amount-update",
    "data" : {
      "id" : "a-portfolio-id",
      "external_id" : "an-external-id",
      "divest_amount" : 1.234,
      "currency" : "a-currency-code"
    }
  }
  ```

<!-- #### Removal

Fully qualified name: `portfolios.removal`

```
{
  "id": "P01F8ZSNV0J45R9DFZ3D7D8C26F"
}
``` -->

<!-- ### Users

#### Creation

Fully qualified name: `users.creation`

```
{
  "id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV", 
  "user_data": {
    "email": "ashok.kumar@example.com", 
    "phone": "+12345667", 
    "first_name": "Ashok", 
    "last_name": "Kumar"
  }
}
```
#### Status update

Fully qualified name: `users.status-update`

```
{
  "id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV", 
  "value": "WAITING_FOR_SIGNATURE"
}
```

#### Account number update

Fully qualified name: `users.account-number-update`

```
{
  "id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV", 
  "value": "BE01234567891234"
}
``` -->

#### Optimisations

##### Status update

Fully qualified name: `optimisations.status-update`

When an optimisation is ready we will notify the b2b client when the optimisation was successfully created.

Fields:

- is_recommended: only when this is “True” the optimisation will be executed (discretionary) or will be proposed to the clientfor acceptance (advisory).

- Possible values for the status field: PENDING, SUCCESS, FAILURE, INFEASIBLE, NOT_OPTIMIZED

!!! info
    In v1.0.0 (aug 2022) only status “SUCCESS” is used.

=== "Event body"
  ```JSON
  {
    "id" : "an-event-id",
    "created" : "-1000000000-01-01T00:00:00Z",
    "version" : "1.0.0",
    "subject" : "optimizations",
    "action" : "status-update",
    "data" : {
      "id" : "an-optimization-id",
      "portfolio_id" : "a-portfolio-id",
      "external_id" : "an-external-id",
      "is_recommended" : true,
      "value" : "SUCCESS"
    }
  ```

##### Owner choice update

Fully qualified name: `optimisations.owner-choice-update`

This event is specifically useful for b2b clients who implemented an advisory flow (advisory mandate).

In this case an optimisation may only be executed when the owner choice has been updated to “ACCEPT”

The id can be used to fetch the optimisation.

- Possible values for the status field are `ACCEPT`, `REJECT`, `REOPTIMIZE`, `IGNORED`

=== "Event body"
  ```JSON
  {
    "id" : "an-event-id",
    "created" : "-1000000000-01-01T00:00:00Z",
    "version" : "1.0.0",
    "subject" : "optimizations",
    "action" : "owner-choice-update",
    "data" : {
      "id" : "an-optimization-id",
      "portfolio_id" : "a-portfolio-id",
      "external_id" : "an-external-id",
      "value" : "ACCEPT"
    }
  }
  ```

<!-- ### Profiles

See also the section on [Suitability Profiler](suitability_profiler.md).

#### Result update

Fully qualified name: `profiles.result-update`

Sent upon completion of ALL Assessments

```
{
  "id": "X01FWDZGKEV3S7SKAX90637H077", 
  "linked_portfolio_id": "P01F8ZSNV0J45R9DFZ3D7D8C26F"
}
```

#### Assessment result update

Fully qualified name: `profiles.assessment-result-update`

Sent upon completion of AN Assessment

```
{
  "id": "X01FWDZGKEV3S7SKAX90637H077", 
  "questionnaire_id": "Q123"
}
```

### Reports

#### Status update

Fully qualified name: `reports.status-update`

- Possible values for the `value` field are READY, FAILED
- Possible values for the `output_format` field are WEB, PDF, VIDEO

```
{
  "id": "R01FWDZGKEV3S7SKAX90637H077",
  "value": "READY", 
  "output_format": "VIDEO"
}
``` 
-->


## REST `/events/` endpoint

### Inbound

#### Deposit event

=== "HTTP"

  ```HTTP hl_lines="1 4"
  POST /events/deposit/ HTTP/1.1
  Host: api.sandbox.investsuite.com
  Content-Type: application/json
  Idempotency-Key: "LVRYWG833Vp2FIIG"

  {
      "data": {
          "amount": "1000",
          "currency": "USD",
          "portfolio": "P01F8ZSNV0J45R9DFZ3D7D8C26F"
      }
  }
  ```

!!! warning
  
    We strongly suggest using the `idempotency-key` to avoid double deposits.




### Outbound

<!-- #### Get events -->

!!! warning

    This is still under development

