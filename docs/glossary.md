---
title: Glossary
---

## Client
You, the bank, the customer of InvestSuite.

## Customer
The customer and the user are synonymous for your (the bank's) customer, the end-user. This is mostly used in functional/business contexts.

## User
The User is synonymous for Customer, but is typically used in technical contexts.

- The User is the owner of one or more Portfolios
- Access to InvestSuite products is controlled on a User level within the InvestSuite console

Refer to [Creating a User](../common_scenarios/account_initiation/#create-a-user).

<!-- #### Document
TODO -->

<!-- ### Report
TODO -->

## Account (of a User)
A counter account that is funded in case of a withdrawal instruction.

An Account is owned by a User.

Refer to [Account Initiation](../common_scenarios/account_initiation/).

## Portfolio (of a User)
An (investment) portfolio is a collection of financial assets grouped with the aim of earning a profit. 
These assets are referred to as **holdings**. Holdings are **positions** in a portfolio. Holdings are the assets out of which a portfolio is composed. 
Holdings can either be **investment products** or **cash holdings**. Cash holdings are expressed in their **currency**.

Alongside holdings, portfolio objects contain general info (eg. portfolio name like 'My Retirement Portfolio', for displaying to the user), and configuration settings to manage the portfolio (eg. a boolean to indicate that trading is allowed, the risk profile the customer had set for the portfolio).

<!-- :question_mark: Holdings == Instruments? -->

A Portfolio is owned by a User.

Refer to [Create a Portfolio](../common_scenarios/account_initiation/#create-a-portfolio).

<!-- Portfolio
Portfolio Stats
:question_mark: not found in InvestSuite API documentation - About 
Contains stats about multiple instruments -->

## Accounts (of a Portfolio)

<!-- :question_mark: relationship to User.Account? -->

Refers to

- Brokerage Account
- (Counter) Bank Account

<!-- Refer to -->

## Risk Profile (of a Portfolio)

See Suitability Profile

## Suitability Profile (of a Portfolio)

We refer to a Suitablity Profile (linked to a Portfolio) as either

- the result of an Appropriateness Test, for Self Investor
- the result of a Suitability Test (for an advisory or discretionary mandate), for Robo Advisor

Refer to [Suitability Profiler](../common_scenarios/suitability_profiler).

## Transaction (of a Portfolio)

We define the following types of transactions:

- Order transaction: one or more trades (= securities movements)
- Corporate Action transaction, eg. stock split or distribution of dividends, can but does not need to include one or more trades (= securities movements)
- Cash Transfer transaction: a deposit of cash in the portfolio, a divestment, fees and taxes
- Security Transfer transaction: at least 1 trade (= securities movement)
- Administrative transaction

<!-- Documentation EOD file storage of transactions in IVS databases - Google Docs -->

## Order (of a Portfolio)

An order sent to the market, resulting in one or more (investment) transactions

## Optimization (of a Portfolio)

The result of an Optimizer run

Refer to [Optimize a portfolio](../common_scenarios/run_optimizer/).