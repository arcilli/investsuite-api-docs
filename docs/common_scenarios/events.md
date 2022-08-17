---
title: Events
---

## Message Bus Events

### Architectural concept

- InvestSuite can post events on the event bus of the client to signal an event
- Any AMQP/MQTT compatible message bus is supported, eg. Kafka, RabbitMQ, ...

### Portfolios

#### Creation

Name: `portfolio.creation`

When IVS supplies the the front-end application to the b2b client they should be notified by IVS when a (real money) portfolio is created. That way, the client can store the IVS portfolio_id at their side and (optionally) link it to the user in their database.

It is up to the b2b client to check if the portfolio is “paper_money” or “real_money” based on this event. This to decide if they need to proceed with setup at their side.

```
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

#### Status update

Name: `portfolio.status-update`

Every time a status of a portfolio gets updated an event will be sent to the b2b client.

Possible values for the status field:
- WAITING_FOR_ACCOUNT: brokerage accounts are to be added by the b2b client
- WAITING_FOR_POLICY: user is still going through the risk profiling, so a policy was not yet determined.
- WAITING_FOR_FUNDS: waiting for incoming funds to arrive. This can be a trigger for the b2b client to push the client to make a transfer if this status isn’t updated within a certain timeframe.
- ACTIVE: portfolio is in use
- INACTIVE

```
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
```

Possible values for the value field:
- WAITING_FOR_POLICY
- WAITING_FOR_ACCOUNT
- WAITING_FOR_FUNDS
- ACTIVE
- INACTIVE

#### Funding

Name: `portfolio.funding`

```
{
  "id": "P01F8ZSNV0J45R9DFZ3D7D8C26F",
  "value": 500, 
  "currency": "EUR"
}
```

#### Withdrawal (divest amount)

Name: `portfolio.withdrawal`

Whenever a user wants to withdraw money, IVS will update the divest amount on the portfolio object.

Every update to this amount will trigger an event towards the b2b client.

Divest amount:

= 1e100 → means full withdrawal.

= specific amount → partial withdrawal

Note: value is the total amount that is requested for withdrawal, not the incremental value (eg. in case of two quickly subsequent withdrawal requests, where the first is not yet settled)

```
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

#### Removal

Name: `portfolio.removal`

```
{
  "id": "P01F8ZSNV0J45R9DFZ3D7D8C26F"
}
```


## REST Event Endpoint

TODO


