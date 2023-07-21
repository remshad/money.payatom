## Introduction:
UPI is a set of APIs created by NPCI to facilitate online immediate payments. UPI is expected to
further propel easy instant payments via mobile. The payments can be both sender (payer) and
receiver (payee) initiated and can be carried out using virtual payment addresses, Aadhaar
integration, mobile number etc. The payer’s smartphone could be used for secure credential
capture.

Merchant on-boarding:
Merchant needs to provide the following information for onboarding of UAT and production
environment:
```Technical list:
1. IP address (For dynamic IPs please provide range of IP addresses)
2. Merchant call-back URL to post final transaction status from our end
```
Once the merchant provides all the above mentioned technical list, we will do the necessary
configuration at their end and provide Merchant ID (MID)  
```
# General Flow:
1. Merchant will send payment collect request on our API. along with Merchant has to send customer’s
upi,amount and other transaction details.
https://github.com/remshad/money.payatom/tree/main/request
2. On receiving request in correct format, we will share upi payment string which needed for qr generation or intent creation.
3. Once the customer pay you will get call back data on provide call back url
https://github.com/remshad/money.payatom/tree/main/call%20back
4. You can confirm the payment by calling polling api and update your ssytem about the payment
```
