### Introduction 
<br/>

This section is meant for:

1.  Service Providers playing the role of Relying Parties, i.e. consuming Authentication Services provided by NDI Authentication Service Provider(ASP), to allow users of their platform to be authenticated via NDI ID
   
2.  Service Providers who are offering to operate as a federated Authentication Service Provider (ASP) to offer Authentication Services to eligible Relying Parties

#### NDI Authentication Service Overview
<br/>

A protected domain refers to a network of computerized systems run by an organization (e.g. Government agency, bank) to manage its digital assets (e.g. customer data, accounts and transactions).  

Digital assets or resources in the protected domain are protected from unauthorized access by cybersecurity measures and access control policies implemented by the organization.

While traditionally protected domains are accessible only by internal applications, organizations are providing partners and apps access to their protected domains through open API.  

To gain access to the protected domain, partners and app developers will have to deal with the organization’s authentication and authorization mechanism, which varies from domain to domain.  

NDI aims to give app developers a standardized authentication and authorization approach, regardless of the protected domain they are accessing.
The ASP is essentially an OpenID Connect Provider (OP), the ASP API is defined based on the OpenID Connect specifications, in the form of a NDI ASP Profile.

Typically, an OP requires users to authenticate themselves using a user id and password.  With NDI, this is replaced with the user’s digital identity in a secure form factor and access to the mobile device hosting the form factor.