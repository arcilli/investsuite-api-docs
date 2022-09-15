---
title: Funding and withdrawal
---

There are two sorts of cash transactions: deposits (funding) and withdrawals. Depending on (1) the direction (fund, withdraw) (2) the setup with the broker (who is integrated: the Client or InvestSuite), and (3) the product (Robo Advisor or Self Investor) the Client Middleware performs several actions. These actions range from moving the money in the broker's account system, to making InvestSuite send a notification. Below we describe in detail which actions to take, and which ones InvestSuite takes depending on the scenario.

For **Robo Advisor**, the Funding/Withdrawal process and the [Rebalancing process](../robo/rebalancing.md) are closely related:

- A *funding* triggers Optimizer, which will generate an Optimization, which contains orders that (during rebalancing) will *invest the cash*;
- A *withdrawal* triggers Optimizer, which will generate an Optimization, which contains orders that (during rebalancing) will *divest instruments and free up cash*.

If you are looking to design/build a middleware that handles both, we recommend to have a look at our [example middleware design](middleware.md).

For **Self Investor**, the Funding/Withdrawal process is more straightforward, as the Customer is responsible for investing/freeing up cash.
## Funding

### Broker integration by InvestSuite

!!! info

    Applies to Robo Advisor, Self Investor

!!! info

    We assume there is an integration between the Broker/Custodian and the Core Banking System (eg. through end-of-day files), that handles the corresponding cash transfers.

1. The **Client Middleware** notifies InvestSuite that the investor account at the Bank has been funded by calling `POST /events/deposit/` (see [here](../concepts/events.md#deposit-event)).
2. **InvestSuite** moves the cash at the broker from the bank's home account to the customer's subaccount. InvestSuite will manage keeping the Transactions, Portfolio Holdings up to date (asynchronously).
3. If this is the first funding, **InvestSuite** sets the `funded_since` field.
4. In case of Robo Advisor, this will (asynchronously) trigger Optimizer.
5. **InvestSuite** sends a notification to the Customer.

### Broker integration by the Client

!!! info

    Applies to Robo Advisor

1. The **Client Middleware** moves the cash at the broker from the bank's home account to the individual's subaccount.
2. Optionally, the **Client Middleware** creates an `EXECUTED` Transaction in InvestSuite, referring to the `transaction_id` of the broker in the `external_id` field (see [here](../concepts/transactions.md#funding)).
3. Once the Transaction is settled at the broker, the **Client Middleware** updates the Transaction status to `SETTLED` (see [here](../concepts/transactions.md#order-settled)).
4. The **Client Middleware** updates the portfolio's cash position (see [here](../concepts/portfolios.md#holdings)). Depending on how the master system (eg. PMS) reflects positions, execute this step earlier in the process (eg. after creating the `EXECUTED` Transaction).
5. In case of Robo Advisor, this will (asynchronously) trigger Optimizer.
6. If the Portfolio was not yet marked as funded, the **Client Middleware** also sets `funded_since` field (see [here](../concepts/portfolios.md#funded-status)).
7. The **Client Middleware** calls `POST /events/deposit/` (see [here](../concepts/events.md#funding-deposit-event)).
8. **InvestSuite** sends a notification to the Customer.

```plantuml
    actor "Customer" as _cus
    participant "InvestSuite" as _ivs
    participant "Client Middleware" as _cmw
    participant "Broker/Custodian" as _bc
    participant "Core Banking System" as _cbs
        
    _cus -> _cbs: Customer funds his investor account
    _cbs -> _cmw: Funding event
    _cmw -> _bc:1. Move cash\nfrom bank's home account to\ncustomer's subaccount
    _bc --> _cmw: external_id
    opt
        _cmw -> _ivs:2. POST /portfolio/{id}/transactions/ with \n- type: CASH_DEPOSIT\n- status: EXECUTED\n- external_id
        _ivs --> _cmw: response
    end
    _bc ->(1) _cmw: Transaction SETTLED
    _cmw -> _ivs:3. PATCH /portfolio/{id}/transactions/{id}/ with \n- status: SETTLED
    _ivs --> _cmw: response
    _cmw -> _ivs: 4. PATCH /portfolio Holdings
    opt if Robo Advisor
        _ivs -> _ivs: 5. Trigger Optimizer (asynchronously)
    end
    _ivs -->_cmw:response
    opt if response.funded_since == null
    _cmw -> _ivs: 6. PATCH /portfolio funded_since
    end
    _cmw -> _ivs: 7. POST /events/deposit
    _ivs -> _cus: 8. Notification
```

## Withdrawal

### Robo Advisor

#### Broker integration by InvestSuite

!!! info

    We assume there is an integration between the Broker/Custodian and the Core Banking System (eg. through end-of-day files), which is master of the `counter_account` and handles the corresponding cash transfers.
 
1. A withdrawal is triggered:
      1. Either by the **Customer**, through the InvestSuite app. The app sets the `divest_amount` on the Portfolio object.
      2. Either by the **Client Middleware**, by setting the `divest_amount` on the Portfolio (see [here](../concepts/portfolios.md#set-divest-amount)).
2. **InvestSuite** asynchronously runs Optimizer, resulting in an Optimization which, during the next rebalancing, will free up cash.
3. In case of an advisory mandate the **Customer** confirms the Optimizaton. The confirmation is registered in the `owner_choice` field of the Optimization object.
4. If there is sufficient cash in the Portfolio:
      1. **InvestSuite** moves the cash at the broker from the customer's subaccount to the bank's home account. InvestSuite will manage keeping the Transactions, Portfolio Holdings and the `divest_amount` up to date (asynchronously). 
      2. The **Core Banking System** transfers the cash to the customer's `counter_account`.
      3. The **Client Middleware** notifies InvestSuite that the payment has occurred (see [here](../concepts/events.md#withdrawal-executed-event)).
      4.  **InvestSuite** sends a notification to the Customer.
5.  If not, the withdrawal will be handled by the **Client Middleware** at a later time, by the rebalancing process or the process that handles executed or settled transactions from the broker. See the [Example Middleware Design](todo.md).


```plantuml
    actor "Customer" as _cus
    participant "InvestSuite" as _ivs
    participant "Client Middleware" as _cmw
    participant "Broker/Custodian" as _bc
    participant "Core Banking System" as _cbs
    
    alt InvestSuite App
        _cus->_ivs:1a. Request withdrawal
    else Middleware
        _cmw->_ivs:1b. Request withdrawal
    end
    _ivs->_ivs:2. Trigger Optimizer (asynchronously)
    opt if Advisory mandate and insufficient cash
        _cus->_ivs:3. Confirm Optimization
    end
    _ivs -> _bc: 4. Get available cash in Portfolio
    alt Sufficient Cash (divest_amount <= cash>)
        _ivs -> _bc: 5a. Move cash from customer's subaccount to bank's home account
        opt This depends on how the Bank and the Broker are integrated
            _bc -> _cbs: Respond to cash movements
            _cbs -> _cbs: 5b. Execute cash transfer\nto the Customer's counter account
            _cbs -> _cmw: Cash transfer EXECUTED
        end
        _cmw -> _ivs: 5c. POST /events/withdraw
        _cus <-_ivs: 5d. Notification
    else Insufficient Cash
        _cmw -> _cmw: 
        note right of _cmw:6. The withdrawal will be handled at a later time,\nby the rebalancing process or the process that\nhandles executed or settled transactions from the broker.
    end
```

#### Broker integration by the Client (Event driven)

1. A withdrawal is triggered:
      1. Either by the **Customer**, through the InvestSuite app. The app sets the `divest_amount` on the Portfolio object.
      2. Either by the **Client Middleware**, by setting the `divest_amount` on the Portfolio (see [here](../concepts/portfolios.md#set-divest-amount)).
2. **InvestSuite** asynchronously runs Optimizer, resulting in an Optimization which, during the next rebalancing, will free up cash.
3. In case of an advisory mandate and insufficient cash, the **Customer** confirms the Optimization. The confirmation is registered in the `owner_choice` field of the Optimization object.
4. The `portfolio.withdrawal-request` event is fired (see [here](../concepts/events.md#withdrawal-request)).
5. If there is sufficient cash in the Portfolio (get this from the Core Banking System or from the Portfolio object):
      1. The **Client Middleware** instructs the **Core Banking System** to execute the cash transfer.
      2. The **Client Middleware** creates a Transaction, type CASH, status SETTLED, and a negative quantity (see [here](../concepts/transactions.md#withdrawal)).
      3. The **Client Middleware** sets the `divest_amount` to 0, indicating there is no more cash to divest (see [here](../concepts/portfolios.md#set-divest-amount)).
      4. The **Client Middleware** updates the Portfolio Holdings with decreased cash (see [here](../concepts/portfolios.md#holdings)).
      5. The **Client Middleware** informs InvestSuite that the withdrawal has executed by calling `POST /events/withdraw/` (see [here](../concepts/events.md#withdrawal-executed-event)).
      6. **InvestSuite** sends a notification to the Customer.
6.  If not, the withdrawal will be handled by the **Client Middleware** at a later time, by the rebalancing process or the process that handles executed or settled transactions from the broker. See the [Example Middleware Design](todo.md).

```plantuml
    actor "Customer" as _cus
    participant "InvestSuite" as _ivs
    participant "Client Middleware" as _cmw
    participant "Broker/Custodian" as _bc
    participant "Core Banking System" as _cbs

    alt InvestSuite App
        _cus->_ivs:1a. Request withdrawal
    else Middleware
        _cmw->_ivs:1b. Request withdrawal
    end
    _ivs->_ivs:2. Trigger Optimizer (asynchronously)
    opt if Advisory mandate and insufficient cash
        _cus->_ivs:3. Confirm Optimization
    end
    _ivs->_cmw: 4. portfolio.withdrawal event\n(NOTE this actually happens in parallel with #2)\nContains divest_amount
    _cmw -> _cbs: 5. Get available cash in Portfolio
    alt Sufficient Cash (divest_amount <= cash>)
        _cmw -> _cbs: 5a. Execute cash transfer
        _cmw -> _ivs: 5b. Create cash transaction\nPOST /portfolios/{id}/transaction with\n- Type CASH\n- Status SETTLED\n- Quantity <0
        _cmw -> _ivs: 5c. Set the divest_amount to 0\nPATCH /portfolios with\n- divest_amount 0
        _cmw -> _ivs: 5d. Update Portfolio holdings\nPATCH /portfolios
        _ivs <- _cmw: 5e. POST /events/withdraw
        _cus <-_ivs: 5f. Notification
    else Insufficient Cash
        _cmw -> _cmw: 
        note right of _cmw: 6. The withdrawal will be handled at a later time,\nby the rebalancing process or the process that\nhandles executed or settled transactions from the broker.
    end
```

#### Broker integration by the Client (Batch process)

1. A withdrawal is triggered:
      1. Either by the **Customer**, through the InvestSuite app. The app sets the `divest_amount` on the Portfolio object.
      2. Either by the **Client Middleware**, by setting the `divest_amount` on the Portfolio (see [here](../concepts/portfolios.md#set-divest-amount)).
2. **InvestSuite** asynchronously runs Optimizer, resulting in an Optimization which, during the next rebalancing, will free up cash.
3. In case of an advisory mandate and insufficient cash, the **Customer** confirms the Optimization. The confirmation is registered in the `owner_choice` field of the Optimization object.
4. In a batch process, the **Client Middleware** gets all portfolios with pending withdrawals (see [here](../concepts/portfolios.md#get-portfolios-with-pending-withdrawals)).
5. If there is sufficient cash in the Portfolio (get this from the Core Banking System or from the Portfolio object):
      1. The **Client Middleware** instructs the **Core Banking System** to execute the cash transfer.
      2. The **Client Middleware** creates a Transaction, type CASH, status SETTLED, and a negative quantity (see [here](../concepts/transactions.md#withdrawal)).
      3. The **Client Middleware** sets the `divest_amount` to 0, indicating there is no more cash to divest (see [here](../concepts/portfolios.md#set-divest-amount)).
      4. The **Client Middleware** updates the Portfolio Holdings with decreased cash (see [here](../concepts/portfolios.md#holdings)).
      5. The **Client Middleware** informs InvestSuite that the withdrawal has executed by calling `POST /events/withdraw/` (see [here](../concepts/events.md#withdrawal-executed-event)).
      6. **InvestSuite** sends a notification to the Customer.
6. If not, the withdrawal will be handled by the **Client Middleware** at a later time, during the next batch job run, by the rebalancing process or the process that handles executed or settled transactions from the broker. See the [Example Middleware Design](todo.md).


```plantuml
    actor "Customer" as _cus
    participant "InvestSuite" as _ivs
    participant "Client Middleware" as _cmw
    participant "Broker/Custodian" as _bc
    participant "Core Banking System" as _cbs
    
    alt InvestSuite App
        _cus->_ivs:1a. Request withdrawal
    else Middleware
        _cmw->_ivs:1b. Request withdrawal
    end
    _ivs->_ivs:2. Trigger Optimizer (asynchronously)
    opt if Advisory mandate and insufficient cash
    _cus->_ivs:3. Confirm sell
    end
    loop Batch process (eg. nightly)
        _cmw -> _ivs: 4. Get portfolios with\npending withdrawals\nGET /portfolios?query=...
        _cmw -> _cbs: 5. Get available cash in Portfolio
        alt Sufficient Cash (divest_amount <= cash>)
            _cmw -> _cbs: 5a. Execute cash transfer
            _cmw -> _ivs: 5b. Create cash transaction\nPOST /portfolios/{id}/transaction with\n- Type CASH\n- Status SETTLED\n- Quantity <0
            _cmw -> _ivs: 5c. Set the divest_amount to 0\nPATCH /portfolios with\n- divest_amount 0
            _cmw -> _ivs: 5d. Update Portfolio holdings\nPATCH /portfolios
            _ivs <- _cmw: 5e. POST /events/withdraw
            _cus <-_ivs: 5f. Notification
        else Insufficient Cash
            _cmw -> _cmw: 
            note right of _cmw:6. The withdrawal will be handled at a later time,\nby the rebalancing process or the process that\nhandles executed or settled transactions from the broker.
        end
    end 
```

### Self Investor

#### Broker integration by InvestSuite

!!! info

    We assume there is an integration between the Broker/Custodian and the Core Banking System (eg. through end-of-day files), which is master of the `counter_account` and handles the corresponding cash transfers.

1. The **Client** issues a withdrawal in the app.
2. **InvestSuite** moves the cash at the broker from the customer's subaccount to the bank's home account. InvestSuite will manage keeping the Transactions and Portfolio Holdings up to date (asynchronously). When this process completes, **InvestSuite** sends a notification to the Customer.
4. The **Core Banking System** transfers the cash to the customer's `counter_account`.