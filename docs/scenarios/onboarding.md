---
title: Onboarding
---

An onboarding process varies wildly from client to client, but typically involves the following steps in any order:

* Strong identify verification (eg. through a national ID provider)
* Capture additional information (Anti Money-Laundering (AML), tax information, ...)
* AML Checks & approval/denial of client
* Setting a withdrawal IBAN
* MiFID Checks (eg. ex-ante costs)
* Legal disclaimers
* Signing a contract
* Suitability test
* Creation of a User in InvestSuite
* Creation of a Portfolio in InvestSuite, with the User as owner

Broadly speaking, the Onboarding process happens either outside the InvestSuite application (Scenario 1, also referred to as 'API Onboarding') or inside InvestSuite (Scenario 2, also referred to as 'In-App Onboarding').

The former is the preferred scenario as it requires less integration work, allows re-use of an existing onboarding solution and provides a smoother experience for your existing users.

## Considerations

- If multiple products are in scope (eg. Robo Advisor and Self Investor), will the customer sign one contract for all products or one contract per product?

|  | One contract | Multiple contracts |
|---|---|---|
| Implications | - Check with legal/compliance whether one contract for multiple mandates is allowed | - Middleware needs to handle product type (more complex) <br> - User status is more complex (to incorporate different products) |

- Will InvestSuite manage the Identity Provider (packaged within the InvestSuite platform) or will the Client (see the Supported 3rd Party list)? <!-- TODO Add Link -->

|  | InvestSuite Managed IdP | Client Managed IdP |
|---|---|---|
| Implications | - How to onboard existing users, if in scope? | - Middleware needs to handle user creation  |

- Will the backoffice systems support the Robo Advisor ['paper money'](../concepts/glossary.md#money) portfolios?

|  | Yes | No |
|---|---|---|
| Implications | - Take special care to not intermingle real and paper money through the integration layer | - Discard the 'portfolio created' event for paper portfolios |

## Scenario 1: API Onboarding

The User onboards outside of InvestSuite: the 'Client Onboarding Solution' takes care of all requirements, and interacts with InvestSuite for two steps:

1. Creating and updating the User
2. Updating the Portfolio

```plantuml source="docs/scenarios/onboarding.puml"
```

Integrations:

- **ONB001 (Outbound)** An HTTP redirect (or webview) to a page which hosts the onboarding solution. This call is unauthenticated.
- **ONB002 (Inbound)** The Onboarding Solution redirects the user back to InvestSuite, either through a HTTP redirect or through a written message.
- **ONB003 (Outbound)** OpenID Connect Sign In flow. Returns a JWT token.
- **ONB004 (Outbound)** An HTTP redirect (or webview) to a page which hosts the contract signature component.
- **USR001 (Inbound)** Create the User. See [here](../concepts/users.md#create-a-user) for the full signature. 
    - If only one contract is used, set the `status` to `ACTIVE`. Otherwise, set it to `WAITING_FOR_SIGNATURE`. 
    - If the IdP is managed by InvestSuite, also specify the [`create_idp_user`](../concepts/users.md#create-a-login) query parameter.
- **USR002 (Inbound)** Update the User status. See [here](../concepts/users.md#update-the-status) for the full signature.
- **PTF001 (Outbound)** An event signals the middleware that a new Portfolio has been created. See [here](../concepts/events.md#creation) for the full signature.
- **PTF002 (Inbound)** Update the Portfolio's brokerage account. See [here](../concepts/portfolios.md#update-the-brokerage-account) for the full signature.

## Scenario 2: In-App Onboarding

Talk to your sales representative.