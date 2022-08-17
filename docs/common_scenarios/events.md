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

**Description**

When IVS supplies the the front-end application to the b2b client they should be notified by IVS when a (real money) portfolio is created. That way, the client can store the IVS portfolio_id at their side and (optionally) link it to the user in their database.

It is up to the b2b client to check if the portfolio is “paper_money” or “real_money” based on this event. This to decide if they need to proceed with setup at their side.

**Example**

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

Name: `status-update`

**Description**

Every time a status of a portfolio gets updated an event will be sent to the b2b client.

Possible statuses:
- WAITING_FOR_ACCOUNT: brokerage accounts are to be added by the b2b client
- WAITING_FOR_POLICY: user is still going through the risk profiling, so a policy was not yet determined.
- WAITING_FOR_FUNDS: waiting for incoming funds to arrive. This can be a trigger for the b2b client to push the client to make a transfer if this status isn’t updated within a certain timeframe.
- ACTIVE: portfolio is in use
- INACTIVE

**Example**

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

Note: possible values for value field are WAITING_FOR_POLICY, WAITING_FOR_ACCOUNT, WAITING_FOR_FUNDS, ACTIVE, INACTIVE


## REST Event Endpoint

TODO


