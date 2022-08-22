---
title: Glossary
---

This page defines the InvestSuite standard terminology that is used throughout our business operations.

## Client, Customer and User
InvestSuite is a B2B4C Company. To avoid confusion between InvestSuite's client and InvestSuite client's client, the following convention is used throughout this documentation:

- **Client**: InvestSuite's customer, typically a bank or financial institution. The B2**B** party.
- **Customer**: the Client's customer, the end-user, typically a person. The B2B4**C** party. This is typically used in functional/business contexts.
- **User**: synonymous for Customer, but typically used in more technical contexts:
    - The User is the owner of one or more Portfolios
    - Access to InvestSuite products is controlled on a User level within the InvestSuite console

Refer to [Creating a User](../common_scenarios/account_initiation/#create-a-user).

<!-- #### Document
TODO -->

<!-- ### Report
TODO -->

## Accounts

- The **Counter Account** of a **user** is the bank account that is funded in case of a withdrawal instruction.

- The **Broker Account** of a **portfolio** is, in an individual account setup, the corresponding account at the broker. This is the account the user funds to fund the portfolio.

Refer to [Account Initiation](../common_scenarios/account_initiation.md).

## Money

- Paper Money (Robo Advisor only): Robo Advisor has a demo mode: the option of a 'virtual portfolio' that uses 'paper money'. The app is fully functional and will show how the portfolio holdings and value evolve, but this is not backed with actual money.
- Real Money: real, actual money (in contrast to paper money).

## Portfolio (of a User)
A Portfolio is a collection of financial assets (instruments). 

A Portfolio also contains metadata (eg. portfolio name like 'My Retirement Portfolio', for displaying to the user), configuration settings to manage the portfolio (eg. a boolean to indicate that trading is allowed, the risk profile the customer had set for the portfolio, ...).

A Portfolio is owned by a User.

A Portfolio is either managed by the User (ie. through Self Investor) or managed for the user (ie. through Robo Advisor).

Refer to [Create a Portfolio](/common_scenarios/portfolios/#create-a-portfolio).

<!-- Portfolio
Portfolio Stats
:question_mark: not found in InvestSuite API documentation - About 
Contains stats about multiple instruments -->

### Holdings, Orders, Transactions and Movements

- **Holdings** are positions (assets) in a portfolio: instruments and/or cash. Cash holdings are expressed in their currency.
- An **order** is sent to the market, resulting in one or more **transactions**.
- Transactions are a combination of **movements** (trade) that describe an exchange or trade.
- A transaction can result in one or more movements, and typically results in an change in portfolio holdings.

!!! Info

    Transactions are used for performance calculation (eg. TWR). 

    Holdings are used for display purposes to the user.

    The two are (by design) not linked, and providing a consistent view to the user needs to be handled by the broker or middleware.

The following types of transactions exist:

- *Order* transactions: buying/selling of instruments, containing one or more trades (movements)
- *Cash* transactions: a deposit of cash, a divestment, fees or tax.
- *Corporate Action* transactions, eg. stock split or distribution of dividends, can but does not need to include one or more trades (movements)
- *Security Transfer* transaction: at least 1 trade (= securities movement)
- *Administrative* transaction

<!-- Documentation EOD file storage of transactions in IVS databases - Google Docs -->

See also [Synchronizing Portfolios](../robo/synchronize_portfolios.md).

## Risk Profile (of a Portfolio)

See Suitability Profile

## Suitability Profile (of a Portfolio)

We refer to a Suitablity Profile (linked to a Portfolio) as either

- the result of an Appropriateness Test, for Self Investor
- the result of a Suitability Test (for an advisory or discretionary mandate), for Robo Advisor

Refer to [Suitability Profiler](../common_scenarios/suitability_profiler).

## Optimization (of a Portfolio)

The result of an Optimizer run

Refer to [Optimize a portfolio](../common_scenarios/run_optimizer/).

## Entities

The business objects of InvestSuite.

Refer to [Business Objects](../common_scenarios/entities.md)