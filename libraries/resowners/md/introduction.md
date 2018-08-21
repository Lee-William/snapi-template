# Introduction
## ASP Integration Overview

The Authentication Service Provider (ASP) is a key component of the NDI platform which performs authentication and authorization.  Client apps accessing resources (API, data) in protected domains (e.g. Government agency, bank systems) may invoke the ASP to authenticate the end-user and obtain the access tokens to access the protected resources.
Client apps invoke the ASP through an interface and interaction flows based on the widely supported OpenID Connect (OIDC) specifications.

The ASP can be federated – it may be run and operated independently from the Government NDI cluster, e.g. a financial institution may run an instance of ASP on its platform to serve the needs of its applications.

This guide focuses on how the ASP may be federated and the integration required.  The following diagram shows an overview of a federated ASP deployment with integration points to on-site and off-site components.

![ASP Overview](\assets\lib\resowners\img\aspoverview.png)

The NDI modules to be deployed in the federated site are:

+ ASP Core – comprising a group of micro-services providing the core features of the ASP

+ Directory Services (Replica) – comprising the NDI User Directory and the Client Registry data stores.

The ASP integrates with the following on-site modules:

+ Authorization Server of the organization, this is the module used by the organization to manage and enforce access control policies on its protected resources.  An IAM product may be used to fulfil this role

+	Relying parties (i.e. client apps) accessing the protected resources of the organization

The integration to these on-site modules will depend on the operating mode which the organization use to run the ASP.
The ASP integrates with the following off-site modules:

+ Directory Services (Master) – to enable replication of data to the Directory Service Replica on the federated site

+ OCSP (Online Certificate Status Protocol) service – to obtain certificate status during user authentication

+ CA CRL distribution point – to download CRL file as a fallback for certificate status check when the OCSP service is unavailable

+ Form Factor Authenticator – to initiate user authentication with the user’s preferred form factor.  The ASP comes with a built-in Form Factor Authenticator for the NDI soft token form factor.  However, if the user’s preferred form factor is an alternative form factor (e.g. crypto-SIM), the ASP will need to route the authentication request to the corresponding Form Factor Authenticator which may be hosted remotely.


## ASP Operating Modes

The ASP may be operated in the 2 modes – as a pure-play authenticator, or as an OIDC provider.  The operating modes will decide how relying parties (i.e. the client app) and the Authorization Server of the federated site interact with the ASP.

### ASP as an Authenticator

![ASP as an Authenticator](\assets\lib\resowners\img\aspauthenticator.png)

The ASP acts as an authenticator service which the Authorization Server calls to perform user authentication with the user’s NDI form factor.  This operation mode is applicable for organizations which are already offering OAuth 2.0 or OIDC based authorization to relying parties accessing their protected resources.  In this scenario, organizations typically use an Authorization Server (or IAM) module to handle the OAuth 2.0/OIDC flows with relying parties.  During the OAuth 2.0/OIDC flow, the Authorization Server module calls the ASP authentication API to perform user authentication with the user’s NDI form factor.  On successful user authentication, the Authorization Server generates and returns an access token to the relying party which it may use to access protected resources.

In this operating mode, the Authorization Server of the organization will integrate with the ASP through the ASP Authentication API.

### ASP as an OpenID Connect Provider(OP)

![ASP as an OIDC Provider](\assets\lib\resowners\img\aspoidcprovider.png)

The ASP acts as an OIDC Provider, handling the OIDC flow with the relying party.  This operating mode is useful for organizations which are planning to expose their capabilities through API and may not have a OAuth 2.0/OIDC enabled Authorization Server.  During the OIDC flow, the ASP performs user authentication with the user’s NDI form factor, on successful authentication, it calls the organization’s Authorization Server to obtain an access token.  It returns the access token to the relying party which may then use it to access protected resources.

In this operating mode, the ASP integrates with the Authorization Server of the organization through the Domain Authorization Interface.

