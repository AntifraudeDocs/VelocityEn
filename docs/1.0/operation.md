---
layout: page-classic-sidebar-left
title: Funcionamento
previous: /docs/1.0/postnotification
next: /docs/1.0/autenticacao
---
---

* O loja integrada ao Pagador, não precisará realizar nenhuma outra integração para que suas transações sejam analisadas no Velocity Check, pois através de uma configuração no Admin Braspag é possível habilitá-la para que suas transações sejam analisadas.  

* Para que seja possível análises em cima de todas as variáveis, é necessário que a loja esteja enviado todos os campos através da integração com o Pagador. Abaixo, de/para de qual campo é necessário o envio a partir da integração entre a loja e a Braspag, através da conectividade Pagador API SOAP ou Pagador API REST.

<table>
    <tr>
        <td></td>
        <td>Velocity Check</td>
        <td colspan="2"><p align="center">Pagador</p></td>
    </tr>
    <tr>
        <td>Descrição Campo</td>
        <td></td>
        <td>API SOAP</td>
        <td>API REST</td>
    </tr>
    <tr>
        <td>Documento do comprador</td>
        <td>CustomerIdentity</td>
        <td>CustomerDataRequest.CustomerIdentity</td>
        <td>Customer.Identity</td>
    </tr>
    <tr>
        <td>E-mail do comprador</td>
        <td>CustomerEmail</td>
        <td>CustomerDataRequest.CustomerEmail</td>
        <td>Customer.Email</td>
    </tr>
    <tr>
        <td>Portador do cartão de crédito</td>
        <td>CustomerName</td>
        <td>CreditCardDataRequest.CardHolder</td>
        <td>Payment.CreditCard.Holder</td>
    </tr>
    <tr>
        <td>Número do cartão de crédito</td>
        <td>CreditCardNumber</td>
        <td>CreditCardDataRequest.CardNumber</td>
        <td>Payment.CreditCard.CardNumber</td>
    </tr>
    <tr>
        <td>CEP do endereço de cobrança</td>
        <td>CustomerPostalCode</td>
        <td>CustomerDataRequest.CustomerAddressData.ZipCode</td>
        <td>Customer.Address.ZipCode</td>
    </tr>
    <tr>
        <td>CEP do endereço de entrega</td>
        <td>ShipPostalCode</td>
        <td>CustomerDataRequest.DeliveryDataRequest.ZipCode</td>
        <td>Customer.DeliveryAddress.ZipCode</td>
    </tr>
    <tr>
        <td>Endereço de IP</td>
        <td>IpAddress</td>
        <td>CustomerDataRequest.IpAddress</td>
        <td>Customer.IpAddress</td>
    </tr>
    <tr>
        <td>Número do Pedido</td>
        <td>OrderId</td>
        <td>OrderDataRequest.OrderId</td>
        <td>MerchantOrderId</td>
    </tr>
</table>

* O Velocity Check realiza análises em cima de regras habilitadas e criadas através do Admin Braspag, para as variáveis abaixo:  

<table>
    <tr><td>Variáveis</td></tr>
    <tr><td>Documento do comprador</td></tr>
    <tr><td>E-mail do comprador</td></tr>
    <tr><td>Portador do cartão de crédito</td></tr>
    <tr><td>Número do cartão de crédito</td></tr>
    <tr><td>CEP do endereço de cobrança</td></tr>
    <tr><td>CEP do endereço de entrega</td></tr>
    <tr><td>Endereço de IP</td></tr>
    <tr><td>Número do Pedido</td></tr>
</table>

* A análise ocorre em cima de cada variável (V), contando quantas vezes (Q) o mesmo passou na Braspag dentro de um determinado período (P)?  

V = Variável  
Q = Quantidade  
P = Período  

Logo,  

V = Número do cartão de crédito  
Q = Máximo de 5 Hits
P = 12 Horas

**Regra = Máximo de 5 Hits de Número do cartão de crédito em 12 Horas**  

Com isso, o Velocity Check ao receber 5 transações (Q) com o mesmo cartão (V) em 12 horas (P), serão aceitas. A partir da 6ª transação com este mesmo número de cartão (V) das outras 5 anteriores, será detectado que a quantidade (Q) excedeu as 5 permitidas no período (P) entre a data da primeira transação recebida e a data desta 6ª transação recebida, terá o status de rejeitada, o cartão irá para quarentena e a resposta terá o conteúdo de que a transação foi bloqueada devido a regra.  

## Quarentena  

Como mencionado acima, o Velocity Check possui uma funcionalidade chamada quarentena, onde é possível configurar valores por tipo de variável para um determinado tempo de expiração através do Admin Braspag.  

* Ao cadastrar uma regra é possível especificar quanto tempo o valor de uma determinada variável irá ser levada em consideração nas próximas análises, ou seja, se o cliente quiser identificar a quantidade de vezes que o mesmo número de cartão se repetiu para um período de 12 horas dentro de um intervalo de 2 dias, não será necessário o Velocity Check realizar esta contagem retroativa agrupando por período.  

Neste cenário por exemplo, a aplicação teria que realizar a contagem para os seguintes intervalos, onde, D é igual a data do dia:  

D-2 = 0h as 12h  
D-2 = 12h as 0h  
D-1 = 0h as 12h  
D-1 = 12h as 0h  

Obs.: No exemplo estamos levando em consideração a hora fechada, mas este cenário deveria levar em consideração o intervalo em milessegundos.  

Com a quarentena, a aplicação não irá realizar essa contagem retroativa por período, pois ao realizar uma análise, será verificado se existe o valor da vaiável em quarentena. Por exemplo: para a regra, Máximo de 5 Hits de Número de cartão de crédito em 12 Horas, o tempo de expiração se definido em 2 dias, a regra será analisada apenas para o período já configurado na regra, ou seja, 12 Horas para traz e irá verificar durante 2 dias se número do cartão se encontra em quarentena.  

* Uma transação analisada e bloqueada pela quarentena, tem o retorno informando que a mesma foi bloqueada pela quarentena.  

## Blacklist

O Velocity Check possui também a funcionalidade de blacklist, onde é possível configurar valores por tipo de variáveis através do Admin Braspag.  

* A transação a ser analisada com o valor de uma determinada variável, e este valor estiver na blacklist, a mesma será bloqueada mesmo que esta transação não seja bloquada pela(s) regra(s) ou quarentena(s) referente a este mesmo valor ou outros valores de outras variáveis.  

* A blacklist possui uma particularidade quanto a dimensão em relação a que loja (base Loja) o valor da variável pertence ou se este valor global (base Braspag), ou seja:  

    - Blacklist por Loja:  

        A loja poderá cadastrar um valor, por exemplo, um determinado número de cartão de crédito na blacklist específica da loja e este valor será usado somente nas análises referentes a sua loja.  

    - Blacklis Global:  

        Esta blacklist é populada através do serviço de chargeback da Braspag, e todas as análises independente da loja, verificam se um determinado valor de uma variável se encontra na blacklist global.  

## Whitelist

O Velocity Check, além da Quarentena e Blacklist, possui também a funcionalidade de WhiteList, onde é possível configurar valores por tipo de variáveis através do Admin Braspag.  

* A transação a ser analisada com o valor de uma determinada variável, e este estiver na whitelist, a mesma será aceita diretamente, independente se alguma regra foi disparada ou não.  




