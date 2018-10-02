### Introduction
<br/>

This section is meant for developers of Web or Mobile Apps, looking to integrate with the NDI Authentication Service Provider (ASP) in order to carry out user authentication with NDI.  Developers may also leverage on the NDI ASP as an OpenID Connect Provider, to obtain access tokens to access API/resources of a protected domain, which could possibly be a Government or commercial entity such as a bank.

#### NDI Authentication Service Overview
<br/>

A protected domain refers to a network of computerized systems run by an organization (e.g. Government agency, bank) to manage its digital assets (e.g. customer data, accounts and transactions).  

Digital assets or resources in the protected domain are protected from unauthorized access by cybersecurity measures and access control policies implemented by the organization.

While traditionally protected domains are accessible only by internal applications, organizations are providing partners and apps access to their protected domains through open API.  

To gain access to the protected domain, partners and app developers will have to deal with the organization’s authentication and authorization mechanism, which varies from domain to domain.  

NDI aims to give app developers a standardized authentication and authorization approach, regardless of the protected domain they are accessing.
The ASP is essentially an OpenID Connect Provider (OP), the ASP API is defined based on the OpenID Connect specifications, in the form of a NDI ASP Profile.

Typically, an OP requires users to authenticate themselves using a user id and password.  With NDI, this is replaced with the user’s digital identity in a secure form factor and access to the mobile device hosting the form factor.