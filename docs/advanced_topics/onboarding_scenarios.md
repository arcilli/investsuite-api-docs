---
title: Onboarding scenarios
---
## Context

Onboarding a user involves the creation of a user in InvestSuite. For the user to start investing, a portfolio needs to be created in InvestSuite with this user as owner. The creation of both the user and the portfolio can be triggered via the InvestSuite API or through the InvestSuite app. Different onboarding scenarios can apply based on your specific implementation of the InvestSuite products. This page explains the most common use cases.

## User creation scenario  1: User onboarding is already done in your application

**Use case description** 

The user onboards via your application. This means kyc checks, aml checks and contract signing are done through your application. Only after this, the user is created at InvestSuite.

**How to create the user in InvestSuite**

The below table shows the minimum required data you provide as an input when creating a user through the InvestSuite API.

Field | Description | How to provide
--- | --- | ---
create_idp_user | Set to `TRUE` if InvestSuite creates the user at the identity provider. | query parameter 
external_id | Your identifier for the user. | request body 
first_name | The first name of the user. Required if the user uses InvestSuite's front-end applications for investing. Displayed in the app. | request body 
last_name | The last name of the user. Required if InvestSuite creates the user at the identity provider. | request body
email | The email address of the user. Required if InvestSuite creates the user at the identity provider. | request body
phone | The mobile phone number of the user. Required if InvestSuite creates the user at the identity provider, only if 2-factor authentication is foreseen. | request body
language | The preferred communication language of the user. | request body
counter_account > bank_account_number_type | Type of the bank account number, typically an IBAN number. Required if the user uses InvestSuite's front-end applications for investing. | request body
counter_account > bank_account_number | Account number of the user to which money withdrawn from the user's portfolio will be settled. Required if the user uses InvestSuite's front-end applications for investing. Displayed in the withdrawal screen of the InvestSuite app. | request body

For the detailed specification of our endpoints, go to [InvestSuite API specification](https://api.sandbox.investsuite.com/redoc). 

## Portfolio creation scenario 1: Create a Self Investor portfolio

**Use case description** 

You create a Self Investor portfolio for a fully onboarded user. The user is already created in InvestSuite.

**How to create the portfolio in InvestSuite**

The below table shows the minimum required data you provide as an input when creating a portfolio through the InvestSuite API.

Field | Description | How to provide
--- | --- | ---
external_id | Your identifier for the portfolio. | request body 
name | The portfolio name. Required if the user uses InvestSuite's front-end applications for investing. Displayed in the InvestSuite app. | request body 
owned_by_user_id | The portfolio owner's user id. | request body
base_currency | The base currency of the portfolio. | request body
money_type | Always `REAL_MONEY` for a Self Investor portfolio. | request body 
config > manager | Always `SELF_MANAGED` for a Self Investor portfolio. | request body
config > manager_version | Always `1` for a Self Investor portfolio. | request body
brokerage_account > bank_account_type | Type of the bank account number, typically an IBAN number. | request body
brokerage_account > bank_account_number | Account number of the account to which the user should transfer money for funding the portfolio. This can be a pooled account or an account specifically for the portfolio. Required if the user uses InvestSuite's front-end applications for investing. Displayed in the funding screen of the InvestSuite app. | request body
brokerage_account > payment_reference | Payment reference the user needs to add to the bank transfer for funding the portfolio. Required if the user uses InvestSuite's front-end applications for investing and the portfolio is funded through a pooled account. Displayed in the funding screen of the InvestSuite app. | request body

For the detailed specification of our endpoints, go to [InvestSuite API specification](https://api.sandbox.investsuite.com/redoc). 