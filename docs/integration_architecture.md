---
title: Integration Architecture
---

The InvestSuite platform is typically integrated with the following 3rd parties:

- **Client backend**, that masters accounts and users and many other things
- A **market data provider**, that provides instrument reference data and quotes
- A **broker/custodian** to execute orders and holds instruments in custody

Depending on the product, some integrations are required (:material-circle:), optional (:material-circle-outline:) or not required/supported (-).

|   | Client Backend | Market Data Provider | Broker/Custodian |
|---|:---:|:---:|:---:|
| Robo Advisor  | :material-circle: | :material-numeric-1-circle-outline: | :material-numeric-2-circle-outline: |
| Self Investor | :material-circle: | :material-circle: | :material-circle: |
| StoryTeller   | :material-numeric-3-circle-outline: | :material-numeric-1-circle-outline: |  - |
| Value Added APIs   | :material-numeric-3-circle-outline: | - | - |
| Portfolio Optimizer API  | :material-circle: | - | - |
| Suitability Profiler API  | :material-circle: | - | - |
| Model Builder  | - | - | - |


## :material-numeric-1-circle-outline: Optional Market Data Provider

If all instrument data can be delivered through our Financial Data API (eg. when the instrument universe is only OTC/unlisted funds), then an integration with a Market Data Provider is not required.
## :material-numeric-2-circle-outline: Robo Advisor with Broker/Custodian Integration

The default way of integrating Robo Advisor is through your backend, which is downstream integrated with the broker/custodian. This allows you to reuse your existing integration, remain in control of the end-to-end flow and avoid an organizational dependency on InvestSuite. This scenario is referred to as 'Integration by the Client'.

Alternatively, we can integrate with a supported broker/custodian. This scenario is referred to as 'Integration by InvestSuite'.

![](../img/robo_integration_options.jpg)

## :material-numeric-3-circle-outline: Optional Client Backend

Data can delivered through an Excel file in the browser.

For more advanced scenarios, an API integration is required.




| Column 1                | Col 2 | Big row span   |
|:-----------------------:|-------| -------------- |
| r1_c1 spans two cols           || One large cell |
| r2_c1 spans two rows    | r2_c2 |                |
|_^                      _| r3_c2 |                |
|    ______ &#20;         | r4_c2 |_              _|