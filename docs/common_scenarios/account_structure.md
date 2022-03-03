---
title: Account structure
---

## Concepts

An InvestSuite User can have one or more Portfolios. A Portfolio consists out of two types of Accounts:

1. Cash Account
2. Instruments Account

At the Financial Institution (the Client), these are typically referred to as an Investment Account and a Securities Account, respectively.
The Securities Account can be at the Client or at a 3rd Party Broker.

## Broker Integration by Client

This scenario applies when the securities account is at the client or if the client performs the broker integration.

REFER TO LEFT SCENARIO

![](/docs/img/accounts1.jpg)

!!! Requirements

    - Each portfolio requires its own dedicated investment and securities account.
    - Only the InvestSuite system may make changes (transactions) on these accounts. The customer is only allowed to fund the investment account, but not initiate outgoing cash transfers or sell/buy transactions. These transactions are reserved for initiation by the InvestSuite platform.  

| InvestSuite Concept | Client Concept | API Reference |
|---|---|---|
| User | Customer | User.external_id
| Portfolio | Investment Account or Securities Account | Portfolio.external_id, Portfolio.brokerage_account
| Cash Amount in the Portfolio | Investment Account | The portfolio field in the Portfolio object lists all positions, including cash. The cash amount is referred to as the currency, eg. "$USD": 1000.
| Instruments in the Portfolio | Securities Account | See above, all other entries.


## Broker is a Supported 3rd Party Broker

This scenario applies when the securities account is held at a 3rd party broker for which InvestSuite offers a supported integration (e.g. Saxo).

REFER TO BROKER INTEGRATION BY INVESTSUITE
IF NOT SUPPORTED SEE 1

![](/docs/img/accounts2.jpg)

1. The Third Party Broker/Custodian holds an _Omnibus account_ (or _Master account_) in the name of the Client. This Omnibus account contains _sub-accounts_ which represent the individual accounts held by the Financial Institution's clients. Typically these sub-accounts are anonymous as the Financial Institution takes on the responsibility of performing regulatory requirements such as KYC and AML.
2. These sub-accounts hold a cash position and instrument positions
3. Each of these sub-accounts maps to an InvestSuite portfolio

In such a setup, dedicated client accounts in the Core Banking system are mostly unnecessary (and sometimes not even supported in the Financial Institution’s systems). In this case, Financial Institutions often do the following:



1. **Alternative 1**: setup a system of virtual accounts. Each portfolio is associated with a dedicated virtual account (with associated virtual IBAN). This makes for easy portfolio funding; bookkeeping and reconciliation
2. **Alternative 2**: setup a pooled account. In this system, one account for all clients and portfolios is put in place. All clients fund to the same account by adding an (un)structured communication referencing the portfolio

The mapping table then looks as follows:


<table>
  <tr>
   <td>InvestSuite 
   </td>
   <td>Financial Institution
   </td>
   <td>Broker/Custodian
   </td>
   <td>InvestSuite API mapping
   </td>
  </tr>
  <tr>
   <td>User
   </td>
   <td>Client
   </td>
   <td>Client
   </td>
   <td>user.external_id
   </td>
  </tr>
  <tr>
   <td>Portfolio
   </td>
   <td>Virtual account
   </td>
   <td>Sub account
   </td>
   <td>Portfolio.external_id
<p>
portfolio.brokerage_account
   </td>
  </tr>
  <tr>
   <td>Cash position in the portfolio
   </td>
   <td>Cash amount on the Virtual account
   </td>
   <td>Cash position in the sub account
   </td>
   <td>
   </td>
  </tr>
  <tr>
   <td>Instrument positions in the portfolio
   </td>
   <td>Instrument positions on the Virtual account
   </td>
   <td>Instrument positions in the sub account
   </td>
   <td>
   </td>
  </tr>
</table>


https://miro.com/app/board/o9J_lUQX6NI=/?moveToWidget=3074457355188901601&cot=14
+1
eyes
raised_hands





14:55
https://miro.com/app/board/o9J_lUQX6NI=/?moveToWidget=3074457355190283340&cot=14
14:55
dit zijn onze huidige flows met omnibus account
14:55
maar zo werken ze dus niet
14:55
die brokerage accounts, zijn dat Belgische rekeningen?
14:56
de reden waarom er doorgaans met omnibus accounts gewerkt wordt, is omdat er geld tussen landen verplaatst moet worden
14:58
bijv. Saxo verplicht de bank om een cash account bij Saxo zelf te hebben (Denemarken als ik mij niet vergis). Als dat geld dan naar de UAE verplaatst moet worden, doen banken dat liefst in één keer (om geen onnodige kosten te hebben).
14:59
Daarvoor dienen die omnibus accounts. Elke dag doet CBD een reconciliation tussen zijn omnibus account bij Saxo en zijn eigen omnibus account
15:00
Als Degroof zegt dat geld rechtstreeks van de portfolio cash account naar de user zijn zichtrekening verplaatst mag worden, dan hebben we dat allemaal niet nodig
15:01
maar die context mis ik dus nog, wat de exacte set-up van Degroof is
15:02
en als dat besproken wordt, zou ik daar graag iemand bij hebben die veel van banking weet, zoals Marc. Want ikzelf ken de impact niet heel goed van individuele accounts vs. omnibus accounts
15:06
ik ben ook niet zeker of ze door hebben dat we een individuele investment account per portfolio vereisen, en dus niet per user