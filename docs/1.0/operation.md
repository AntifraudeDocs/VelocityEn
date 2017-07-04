---
layout: page-classic-sidebar-left
title: Operation
previous: /docs/1.0/overview
next: /docs/1.0/operation
---
---

* The integrated merchant to the Pgador, does not have to carry out any other integration so that its transactions are analyzed in Velocity Check, because through a configuration in the Admin Braspag it is possible to enable it so that its transactions are analyzed.  

* In order to be able to analyze over all variables, it is necessary for the merchant to be sent all fields through integration with the Pagador. Below, from/to from which field is needed the sending from the integration between the merchant and Braspag, through the connectivity Pagador API SOAP or Pagador API REST.

<table>
    <tr>
        <td></td>
        <td>Velocity Check</td>
        <td colspan="2"><p align="center">Pagador</p></td>
    </tr>
    <tr>
        <td>Field Description</td>
        <td></td>
        <td>API SOAP</td>
        <td>API REST</td>
    </tr>
    <tr>
        <td>Customer document</td>
        <td>CustomerIdentity</td>
        <td>CustomerDataRequest.CustomerIdentity</td>
        <td>Customer.Identity</td>
    </tr>
    <tr>
        <td>Customer e-mail</td>
        <td>CustomerEmail</td>
        <td>CustomerDataRequest.CustomerEmail</td>
        <td>Customer.Email</td>
    </tr>
    <tr>
        <td>Credit card holder</td>
        <td>CustomerName</td>
        <td>CreditCardDataRequest.CardHolder</td>
        <td>Payment.CreditCard.Holder</td>
    </tr>
    <tr>
        <td>Credit card number</td>
        <td>CreditCardNumber</td>
        <td>CreditCardDataRequest.CardNumber</td>
        <td>Payment.CreditCard.CardNumber</td>
    </tr>
    <tr>
        <td>Billing address zipcode</td>
        <td>CustomerPostalCode</td>
        <td>CustomerDataRequest.CustomerAddressData.ZipCode</td>
        <td>Customer.Address.ZipCode</td>
    </tr>
    <tr>
        <td>Shipping address zipcode</td>
        <td>ShipPostalCode</td>
        <td>CustomerDataRequest.DeliveryDataRequest.ZipCode</td>
        <td>Customer.DeliveryAddress.ZipCode</td>
    </tr>
    <tr>
        <td>IP address</td>
        <td>IpAddress</td>
        <td>CustomerDataRequest.IpAddress</td>
        <td>Customer.IpAddress</td>
    </tr>
    <tr>
        <td>Order number</td>
        <td>OrderId</td>
        <td>OrderDataRequest.OrderId</td>
        <td>MerchantOrderId</td>
    </tr>
</table>

* Velocity Check performs analyzes on rules enabled and created through Admin Braspag, for the variables below:  

<table>
    <tr><td>Valiables</td></tr>
    <tr><td>Customer document</td></tr>
    <tr><td>Customer e-mail</td></tr>
    <tr><td>Credit card holder</td></tr>
    <tr><td>Credit card number</td></tr>
    <tr><td>Billing address zipcode</td></tr>
    <tr><td>Shipping address zipcode</td></tr>
    <tr><td>>IP address</td></tr>
    <tr><td>Order number</td></tr>
</table>

* The analysis takes place over each variable (V), counting how many times (Q) it spent in Braspag within a certain period (P)?  

V = Variable  
Q = Quantity  
P = Period  

Next,  

V = Credi card number  
Q = Maximum 5 Hits
P = 12 Hours

**Rule = Máximo de 5 Hits de Número do cartão de crédito em 12 Horas**  

With this, the Velocity Check when receiving 5 transactions (Q) with the same card (V) in 12 hours (P), will be accepted. From the 6th transaction with this same card number (V) of the previous 5, it will be detected that the quantity (Q) exceeded the allowed 5 in the period (P) between the date of the first transaction received and the date of this 6th transaction received , Will have the status of rejected, the card will go to quarantine and the response will have the content that the transaction was blocked due to rule.  

## Quarentine  

As mentioned above, Velocity Check has a feature called quarantine, where you can set values by type of variable for a certain expiration time through Admin Braspag.  

* When registering a rule it is possible to specify how long the value of a particular variable will be taken into account in the next analysis, that is, if the customer wants to identify the number of times the same card number was repeated for a period of 12 hours Within 2 days, it will not be necessary for Velocity Check to perform this retroactive counting by grouping by period.  

In this scenario for example, the application would have to perform the count for the following ranges, where, D is equal to date of the day:  

D-2 = 0h as 12h  
D-2 = 12h as 0h  
D-1 = 0h as 12h  
D-1 = 12h as 0h  

Note.: In the example we are taking the closed hour into account, but this scenario should take into account the interval in milliseconds.  

With quarantine, the application will not perform this counting back by period, because when performing an analysis, it will be checked if there is the value of the quarantined vaável. For example: for the rule, Maximum of 5 Hits of Credit Card Number in 12 Hours, the expiration time if set in 2 days, the rule will be analyzed only for the period already configured in the rule, ie 12 Hours for Brings and will check for 2 days if the card number is in quarantine.  

* A transaction that has been quarantined and quarantined has a return that the quarantine blocked it.  

## Blacklist

Velocity Check also has blacklist functionality, where you can configure values by type of variables through Admin Braspag.  

* The transaction to be analyzed with the value of a certain variable, and this value is in the blacklist, it will be blocked even if this transaction is not blocked by the rule (s) or quarantine (s) referring to this same value or other values of other variables.  

* The blacklist has a particularity as to the size in relation to which merchant (merchant base) the value of the variable belongs or if this global value (base Braspag), ie:  

    - Blacklist by merchant:  

        The merchant may enter a value, for example, a certain credit card number on the merchant specific blacklist and this value will be used only in the analyzes for your merchant.  

    - Global blacklis:  

        This blacklist is populated by Braspag's chargeback service, and all merchant independent reviews verify that a particular value of a variable is in the global blacklist.  

## Whitelist

Velocity Check, in addition to Quarantine and Blacklist, also has the functionality of WhiteList, where you can configure values by type of variables through Admin Braspag.  

* The transaction to be analyzed with the value of a particular variable, and this is in the whitelist, it will be accepted directly, regardless of whether any rules were triggered or not.  