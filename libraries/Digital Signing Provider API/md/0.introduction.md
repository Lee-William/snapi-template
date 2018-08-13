# Release History

| Date | Revision | By | Comments |
| ------ | -- | ----------- | ------------------- |
| 23-July-2018   | 0.1 | Yee Wee LOW | First Draft |

# Introduction

This technical guide is meant for developers of web-based Signature Applications (Web App accessed by users through the desktop or mobile browsers) to interface with the NDI HSS, so that citizens can use their NDI digital identity for signing.  

![Hash signing Overview](..\img\hsoverview.png)


The definition of “electronic signature” contained in Electronic Transactions Act (ETA2010) facilitated the recognition of data in electronic form in the same manner as a hand-written signature satisfies those requirements for paper-based data. In the context of NDI, the present document focuses on electronic signatures created by cryptographic means in a “secure signature creation application”. Typically, this involves a smart card and a card reader with sufficient processing power and display capabilities to present full details of the transactions to be “signed”.

An alternative approach is to capitalize on the fact that many citizens already possess a smart device which has strong processing power and large display – smart mobile phone. In fact, a large proportion of citizens own a smart mobile phone, which is capable of running mobile application (IOS/Android), which represents the nature choice for implementation of a socially-inclusive, electronic signature solution for the majority of citizens.

“Mobile Digital Signatures” challenges include “interoperability” issues as restraining factor, requiring standardisation to avoid market fragmentation.

In considering the use of mobile digital signature, we consider only the process of forming an electronic signature in relation to a message digest presented to the citizen. It specifically excludes application level control concerning the signed message.

Provision of a mobile signature indicates only that the citizen would like to proceed with a transaction as presented, regardless of whether the citizen is allowed/entitled to do so.

## Hash Signing Service

Coordination and management of the digital signature process represents an opportunity to define a Hash Signing Service for citizens and application providers alike. Such an approach might:

+ Accelerate adoption of digital signature by Digital Signature Application Providers (DSAP) (and consequently adoption by citizens).
  
+ Allow implementation/deployment of a standardised API

+ Permit access to an existing base of endusers possessing SingPass accounts

+ Coordinate activation of digital signature functionality for endusers
  
+ Coordinate the processing of signature requests for DSAP

+ Provide a manageable approach to risk reduction
  
NDI HSS provides a standardised means to form a digital signature using NDI-provisioned digital identity (using form factors such as NDI soft token)
The HSS is essentially a signing gateway, the API is defined to standardise the communication with the signature application:

1. Signature request/issued

2. Signature response/received

Typically, this means that a user will trigger the signing request from the signature application, HSS will relay the request to the user’s registered mobile device, where the user can use the NDI provisioned digital identity to create a digital signature.

## A Mobile Digital Signing Flow

![Mobile Digital Signing Flow](..\img\mobiledsflow.png)

The Digital Signature Application Provider (DSAP) may choose to use NDI to authenticate the user, so that the correct document is presented to the user. However, this is not discussed in the scope of the present document. You may refer to “Technical Guide – Client App (Web)” for the details.

1. It is recommended to authenticate the user using NDI, for high risk transaction. An OIDC id token and access token will be issued, which you must use to access HSS APIs. (See “Technical Guide – Client App (Web)” for the details.)

2. If you are not authenticating the user using NDI, you have to obtain a client credential grant from the ASP Authorisation Endpoint using your client info. 

You should collect NDI Id from the user through the client app,so that together with security tokens obtain in “Gain access”, are provided when composing the Sign Request. 

The HSS signing flow mechanism utilizes a distributed approach as follows:
+ On receiving the sign request, the HSS obtains the end-user’s form factor attributes from the NDI User Directory, based on the NDI id.  The form factor attributes consist of the end-user’s preferred form factor type, the form factor address (e.g. push notification id), etc.  In this case, the end-user’s preferred form factor is the NDI Soft Token. The HSS then routes the sign notification via push notification to the NDI App residing on the end-user’s mobile device where the soft token form factor resides.

+ The end-user receives the push notification on his mobile device, inspects the description (e.g. name of the client app and scope of access requested) and accepts the sign request.  In accepting the sign request, the end-user may have to enter a PIN (or use phone-specific biometric) to unlock the soft token.

+ The soft token verifies the sign request, generates a signature value with the end-user’s private key and returns the sign response to the HSS via the NDI App. 

+ The HSS verifies the signature of the signed response with the end-user’s digital certificate, after checking the integrity of digital certificate by verifying the certificate chain of trust, and the status of the digital certificate with the CA’s Online Certificate Status Protocol (OCSP) service.

+ On successful verification, the Web App receives the sign response from the HSS via redirect URI provided in the sign request or client registry.

+ The Web App uses the signature, user’s certificate, and OCSP responses within the sign response, to construct the required signature structure and embed it into the document.
