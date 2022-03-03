---
title: Account structure
---

## Concepts

An InvestSuite User can have one or more Portfolios. A Portfolio contains two types of holdings:

1. Cash
2. Instruments (securities)

At the Financial Institution (the Client), these are typically hald in an Investment Account (Cash Account) and a Securities Account, respectively.

The Investment Account can be at the Client or at a 3rd Party (FYI WHICH IS THE CASE FOR DEGROOF, WITH ING).

The Securities Account can be at the Client or at a 3rd Party Broker.

These accounts are either individual or omnibus (master account) with sub-accounts.

## Individual Accounts

<!-- This scenario applies when the securities account is at the client or if the client performs the broker integration. -->
<!-- REFER TO LEFT SCENARIO -->

!!! note "Applies to"
    - Robo Advisor
    - Self Investor (??? Impact to be assessed) <-- this is the case for Degroof
    - 'Broker Integration by the Client' Scenario (TBD)

!!! warning "Requirements"
    - This requires individual accounts per Portfolio (not per User)
    - Only the InvestSuite system may make changes (transactions) on these accounts. The customer is only allowed to fund the investment account, but not initiate outgoing cash transfers or sell/buy transactions. These transactions are reserved for initiation by the InvestSuite platform.  

![](/docs/img/accounts1.jpg)

| InvestSuite Concept | Client Concept | API Reference |
|---|---|---|
| User | Customer | User.external_id
| Portfolio | Investment Account or Securities Account | Portfolio.external_id, Portfolio.brokerage_account TODO WHAT IS WHAT??
| Cash Amount in the Portfolio | Investment Account | The portfolio field in the Portfolio object lists all positions, including cash. The cash amount is referred to as the currency, eg. "$USD": 1000.
| Instruments in the Portfolio | Securities Account | See above, all other entries.

## Omnibus Account at a supported 3rd Party Broker (eg. Saxo)

!!! note "Applies to"
    - Robo Advisor (?? have we already done this?)
    - Self Investor

!!! warning "Requirements"
    - Broker is a Supported 3rd Party Broker (eg. Saxo)

<!-- This scenario applies when the securities account is held at a 3rd party broker for which InvestSuite offers a supported integration (e.g. Saxo). -->
<!-- REFER TO BROKER INTEGRATION BY INVESTSUITE -->
<!-- IF NOT SUPPORTED SEE 1 -->

![](/docs/img/accounts2.jpg)

- The Third Party Broker/Custodian holds an _Omnibus account_ (or _Master account_) in the name of the Client. This Omnibus account contains _sub-accounts_ which represent the individual accounts held by the Financial Institution's clients (= the Customer). Typically these sub-accounts are anonymous as the Financial Institution takes on the responsibility of performing regulatory requirements such as KYC and AML.
- These sub-accounts hold a cash position and instrument positions

In such a setup, dedicated Customer accounts in the Client's Core Banking system are mostly unnecessary (and sometimes not even supported in the Financial Institution’s systems). In this case, Financial Institutions has two options:

1. **Alternative 1**: setup a system of virtual accounts. Each portfolio is associated with a dedicated virtual account (with associated virtual IBAN). This makes for easy portfolio funding; bookkeeping and reconciliation
2. **Alternative 2**: setup a pooled account. In this system, one account for all clients and portfolios is put in place. All clients fund to the same account by adding an (un)structured communication referencing the portfolio

TODO WHAT IS THE IMPACT FOR US?

The mapping table then looks as follows:

| InvestSuite Concept | Client Concept | Broker/Custodian | API Reference |
|---|---|---|---|
| User | Customer | Customer | User.external_id
| Portfolio | Virtual account OR pooled account | Sub account | Portfolio.external_id, Portfolio.brokerage_account TODO WHAT IS WHAT??
| Cash Amount in the Portfolio | Cash amount on the virtual account or pooled account | Cash position in the sub account | The portfolio field in the Portfolio object lists all positions, including cash. The cash amount is referred to as the currency, eg. "$USD": 1000. (TODO TO CHECK ???)
| Instruments in the Portfolio | Instrument positions on the virtual account or pooled account | Instrument positions in the virtual account or sub account | See above, all other entries.


### Individual Account - Funding Process

TODO
### Individual Account Withdrawal Process

TODO
### Omnibus Account - Funding Process

TODO -- See [miro diagram of melanie](https://miro.com/app/board/o9J_lUQX6NI=/?moveToWidget=3074457355188901601&cot=14)

### Omnibus Account -- Withdrawal Process

TODO -- See [miro diagram of melanie](https://miro.com/app/board/o9J_lUQX6NI=/?moveToWidget=3074457355190283340&cot=14)
