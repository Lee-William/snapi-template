#### Introduction
<br/>

This section is meant for developers of web-based signature applications (Web App accessed by users through the desktop or mobile browsers) to interface with the NDI Hash Signing Service(HSS), so that citizens can use their NDI digital identity for signing.  

![Hash signing Overview](/assets/lib/trusted-access/resowners/img/hsoverview.png)

The definition of “electronic signature” contained in Electronic Transactions Act (ETA2010) facilitated the recognition of data in electronic form in the same manner as a hand-written signature satisfies those requirements for paper-based data. In the context of NDI, the present document focuses on electronic signatures created by cryptographic means in a “secure signature creation application”. Typically, this involves a smart card and a card reader with sufficient processing power and display capabilities to present full details of the transactions to be “signed”.

An alternative approach is to capitalize on the fact that many citizens already possess a smart device which has strong processing power and large display – smart mobile phone. In fact, a large proportion of citizens own a smart mobile phone, which is capable of running mobile application (IOS/Android), which represents the nature choice for implementation of a socially-inclusive, electronic signature solution for the majority of citizens.

“Mobile Digital Signatures” challenges include “interoperability” issues as restraining factor, requiring standardisation to avoid market fragmentation.

In considering the use of mobile digital signature, we consider only the process of forming an electronic signature in relation to a message digest presented to the citizen. It specifically excludes application level control concerning the signed message.

Provision of a mobile signature indicates only that the citizen would like to proceed with a transaction as presented, regardless of whether the citizen is allowed/entitled to do so.

#### Hash Signing Service

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