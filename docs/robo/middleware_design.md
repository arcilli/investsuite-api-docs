---
title: Middleware design
---

## Context

The [quick start](quick_start.md) is a good place to start to get acquainted with the Robo Advisor API. However it is a partial and happy-path flow.

This page lists an *example* middleware design to take care of all aspects of Robo Advisor beyond [Onboarding](../scenarios/onboarding.md):

- [Funding](../scenarios/cash_movements.md#broker-integration-by-the-client)
- Withdrawals ([event-driven](../scenarios/cash_movements.md#broker-integration-by-the-client-event-driven) or [batch](../scenarios/cash_movements.md#broker-integration-by-the-client-batch-process))
- [Rebalancing](rebalancing.md) (periodic, triggered by funding or withdrawal request)
- Handle executed/settled Transactions

## Example middleware design

<iframe width="100%" height="640" src="https://miro.com/app/live-embed/uXjVPbHnms8=/?moveToViewport=-1764,-1577,2749,1491&embedId=784475785527" frameborder="0" scrolling="no" allowfullscreen></iframe>

Integrations listed in the diagram:

- **GEPO01**: Get all Portfolios with `GET /portfolios`. See [paginating on business objects](../concepts/entities.md#pagination).

- **GEPO02**: See [get Portfolios with a pending withdrawal request](../concepts/portfolios.md#get-portfolios-with-pending-withdrawals)

- **GEOP01**: See [get Optimization for a Portfolio](optimization.md#get-the-latest-optimization-of-a-portfolio)

- **POTR01**: See [create `PLACED` order](../concepts/transactions.md#order-placed)

- **PATR01**: See [update Transaction](../concepts/transactions.md#update-transaction)

- **PAPO01**: See [set `divest_amount`](../concepts/portfolios.md#set-divest-amount)

- **PAPO02**: See [update Portfolio Holdings](../concepts/portfolios.md#holdings_1)

- **PAPO03**: See [set Portfolio Funded](../concepts/portfolios.md#funded-status)

- **POEV01**: See [funding event](../concepts/events.md#funding-deposit-event)

- **POEV02**: See [withdrawal executed event](../concepts/events.md#withdrawal-executed-event)