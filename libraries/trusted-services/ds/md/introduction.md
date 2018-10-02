### Introduction
<br/>

This technical guide is meant for developers of web-based Signature Applications (Web App accessed by users through the desktop or mobile browsers) to interface with the NDI HSS, so that citizens can use their NDI digital identity for signing.

![Hash signing Overview](/assets/lib/trusted-services/ds/img/hsoverview.png)

The definition of “electronic signature” contained in Electronic Transactions Act (ETA2010) facilitated the recognition of data in electronic form in the same manner as a hand-written signature satisfies those requirements for paper-based data. In the context of NDI, the present document focuses on electronic signatures created by cryptographic means in a “secure signature creation application”. Typically, this involves a smart card and a card reader with sufficient processing power and display capabilities to present full details of the transactions to be “signed”. 

An alternative approach is to capitalize on the fact that many citizens already possess a smart device which has strong processing power and large display – smart mobile phone. In fact, a large proportion of citizens own a smart mobile phone, which is capable of running mobile application (IOS/Android), which represents the nature choice for implementation of a socially-inclusive, electronic signature solution for the majority of citizens.

“Mobile Digital Signatures” challenges include “interoperability” issues as restraining factor, requiring standardisation to avoid market fragmentation. 

In considering the use of mobile digital signature, we consider only the process of forming an electronic signature in relation to a message digest presented to the citizen. It specifically excludes application level control concerning the signed message.

Provision of a mobile signature indicates only that the citizen would like to proceed with a transaction as presented, regardless of whether the citizen is allowed/entitled to do so.

