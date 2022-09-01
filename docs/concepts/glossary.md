---
title: Glossary
---

This page defines the InvestSuite standard terminology.

## Client, Customer and User
InvestSuite is a B2B4C Company. To avoid confusion between InvestSuite's client and InvestSuite client's client, the following convention is used throughout this documentation:

- **Client**: InvestSuite's customer, typically a bank or financial institution. The B2**B** party.
- **Customer**: the Client's customer, the end-user, typically a person. The B2B4**C** party. This is typically used in functional/business contexts.
- **User**: synonymous for Customer, but typically used in more technical contexts:
    - The User is the owner of one or more Portfolios
    - Access to InvestSuite products is controlled on a User level within the InvestSuite console

Refer to [Creating a User](users.md#create-a-user).

<!-- #### Document
TODO -->

<!-- ### Report
TODO -->

## Accounts
- The **Counter Account** of a **user** is the bank account that is funded in case of a withdrawal instruction. This can be at a 3rd party bank.

- The **Broker Account** of a **portfolio** (also known as the **investor account**) is, in an individual account setup, the corresponding account at the broker. This is the account the user funds to fund the portfolio.

<!-- TODO quid 'investor account' at the bank -->

Refer to [Account Initiation](../common_scenarios/account_initiation.md). 
<!-- TODO -->


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
- **Holdings** are positions (or assets) in a portfolio: instruments and/or cash. Cash holdings are expressed in their currency.
- An **order** is sent to the market, resulting in one or more transactions.
- **Transactions** are a combination of movements that describe an exchange or trade.
- A **movement** (or trade) is a change that impacts the holdings of a portfolio.

!!! info "How are transactions and holdings related?"

    Transactions are used for performance and return calculation (eg. TWR) on a portfolio. 

    Holdings are used to display to the user what is inside the portfolio.

    The two are (by design) not linked, and providing a consistent view to the user needs to be handled by the broker or middleware.

<!-- Documentation EOD file storage of transactions in IVS databases - Google Docs -->
<!-- See also [Synchronizing Portfolios](../robo/synchronize_portfolios.md). -->
Refer to [Transactions](transactions.md).

## Suitability Profile, Risk Profile (of a Portfolio)
We refer to a Suitablity Profile (linked to a Portfolio) as either

- the result of an Appropriateness Test, for Self Investor
- the result of a Suitability Test (for an advisory or discretionary mandate), for Robo Advisor

Refer to [Suitability Profiler](../scenarios/suitability_profiler.md).

## Optimization (of a Portfolio)
The result of an Optimizer run

Refer to [Optimize a portfolio](../common_scenarios/run_optimizer/).

## Entities
The business objects of InvestSuite.

Refer to [Business Objects](entities.md).

## Rebalancing
Generating orders to modify a portfolio to bring it back into an optimal state or in line with the model portfolio.

Refer to [Rebalancing](../robo/rebalancing.md).

## Reconciliation
The process of ensuring the portfolio holdings and transactions are in sync between the broker/custodian and Robo Advisor.

## Investment Strategy, Policy
An Investment Strategy (also known as a Policy) refers to a set of principles designed to help an individual investor achieve their financial and investment goals.

This is part of the input for Optimizer to construct the Portfolio.

## Horizon
The investment horizon. The total length of time that an investor expects to hold a security or a portfolio.

This is part of the input for Optimizer to construct the Portfolio.

## Mandate

- **Discretionary** (Robo Advisor only): No action from the user required to execute the recommended orders.
- **Advisory** (Robo Advisor only): The user needs to approve the recommended optimization before the orders can be executed.
- **Execution-only** (Self Investor only): The user decides what orders to execute.



<!-- 
Risk profile = the user is assessed to define a suitable risk profile. The risk profile reflects the user’s attitude to risk and therefore the types of financial instruments (and the risks attached to them) that are suitable for them (=investment policy) 

Suitability Profiler = the component that captures all information from a user, to create a suitable portfolio. This includes the risk profile, but also their goal, other preferences, … 

Asset Allocation: the relative distribution between assets, such as stocks, bonds, … of a model portfolio 

Optimizer Run: the process of optimizing a portfolio, including a call to GOE and the subsequent portfolio construction or modification (see Rebalancing) 

Optimization = an object that is the result of an Optimizer Run. It contains information like the orders to execute and goal-related data received from GOE. 


Order orchestration = executing the actual orders with the broker. This is different from rebalancing, where the orders are generated in the InvestSuite system, but no communication with the broker happens yet. This is not in scope for the solution with Franklin Templeton, but will be relevant for Clients that buy the solution.  -->