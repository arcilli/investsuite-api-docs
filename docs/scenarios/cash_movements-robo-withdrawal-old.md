NOTE: This is the old withdrawal flow, that intermingles Withdrawal and Rebalancing in Robo

This file can be deleted once the updated flows are battle tested.

#### Broker integration by the Client (Event driven)

Steps to take when the clients issues an instruction to withdraw funds, and you as the Client are in charge of the integration with the broker:

1. The **Customer** (through the InvestSuite app) requests a withdrawal. <!-- The `portfolios.withdrawal` event is fired (see [here](../concepts/events.md#withdrawal-request)) - BUT THIS IS NOT PART OF THE MAIN FLOW HENCE COMMENTED OUT -->
2. **InvestSuite** asynchronously performs a portfolio optimisation, resulting in one or more orders to free up cash.
3. The `optimisations.status-update` event is fired (see [here](../concepts/events.md#status-update_1)).


4. If the Optimisation is recommended (included in the event body), The **Client Middleware** responds to by getting the full optimisation (see [here](../robo/optimization.md#get-the-latest-optimization-of-a-portfolio)).
5. **You** place the sell orders at the broker.
6. **You** transfer the freed up cash from the broker to the client's `counter_account`. **See below**: Get counter account.
7. **You** notify InvestSuite that the payment has occurred. **See below**: Notify InvestSuite on successful cash transfer.
8. **InvestSuite** puts the message on a queue to send a push notification to the client.
9.  **You** create a `SETTLED` transaction, referring to the transaction ID created by the broker in the `external_id` attribute. **See below**: Create transaction.
10. **You** update the portfolio's cash position. **See below**: Update cash position.
11. **You** reset the divest amount to notify InvestSuite that the cash that became available in the portfolio is ready to be invested.  **See below**: Reset divest amount.

<!-- TODO ADVISORY MANDATE -->

<!-- if event -> GET PORTFOLIO. If cash holidings >= divest amount, execute the transfer / patch holdings / post transactions. If not - wait. -->
<!-- 3. In case of a advisory mandate (as opposed to a discretionary mandate, see [Portfolio creation](/common_scenarios/account_initiation/#create-a-portfolio)) the user confirms the sell orders. The confirmation is registered in the `owner_choice` field of the Optimization object. -->


```plantuml
    actor "Customer" as _cus
    participant "InvestSuite" as _ivs
    participant "Bank with\nClient Middleware" as _cmw
    participant "Broker/Custodian" as _bc

    _cus->_ivs:1. Request withdrawal
    _ivs->_ivs:2. Optimize Portfolio
    _ivs->(1)_cmw: 3. optimisations.status-update event
    _ivs<-_cmw: 4. GET /optimisation
    _ivs-->_cmw:reply, containing is_recommended
    _cmw->_bc:5. Place orders

    loop while there are still orders pending
    _cmw<-_bc: Update Transaction status
    opt
    _ivs<-_cmw:6. Update Portfolio Transactions & Holdings (EXECUTED)
    end
    end


    _cmw->_cmw:7. Execute cash transfer to\nCustomer's counter account
    _ivs<-_cmw:6. Update Portfolio Transactions & Holdings (SETTLED)
    _cmw->_ivs:6. Set divest_amount to 0
    _ivs<-_cmw:8. POST /events/withdraw
    _cus <-_ivs:9. Notification
```