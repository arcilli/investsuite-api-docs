---
title: Integration Architecture
---

The InvestSuite platform is typically integrated with the following 3rd parties:

- **Your backend**, that masters accounts and users and many other things
- A **market data provider**, that provides instrument reference data and quotes
- A **broker/custodian** to execute orders and holds instruments in custody

Depending on the product, some integrations are required (:material-circle:), optional (:material-circle-outline:) or not required/supported (-).

|   | Your Backend | Market Data Provider | Broker/Custodian |
|---|:---:|:---:|:---:|
| Robo Advisor  | :material-circle: | :material-circle: | :material-numeric-1-circle-outline: |
| Self Investor | :material-circle: | :material-circle: | :material-circle: |
| Storyteller   | :material-circle: | - |  - |
| Value Added APIs   | :material-circle: | - | - |

## :material-numeric-1-circle-outline: Robo Advisor with Broker/Custodian Integration

The default way of integrating Robo Advisor is through your backend, which is downstream integrated with the broker/custodian. This allows you to remain in control of the entire integration and avoid an organizational dependency on InvestSuite. This corresponds to Option A in the diagram.

Alternatively, we can integrate with a supported broker/custodian. This corresponds to Option B in the diagram.

- REASONS FOR CHOOSING A ???
- REASONS FOR CHOOSING B ???
- DO WE HAVE A SEXY NAME FOR OPTIONS A AND B? LIGHT INTEGRATION? DIRECT INTEGRATION?

![](../img/robo_integration_options.jpg)



| Column 1                | Col 2 | Big row span   |
|:-----------------------:|-------| -------------- |
| r1_c1 spans two cols           || One large cell |
| r2_c1 spans two rows    | r2_c2 |                |
|_^                      _| r3_c2 |                |
|    ______ &#20;         | r4_c2 |_              _|