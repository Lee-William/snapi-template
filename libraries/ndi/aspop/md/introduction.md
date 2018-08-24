# Introduction

The Authentication Service Provider (ASP) is a key component of the NDI platform which performs authentication and authorization.  Client apps accessing resources (API, data) in protected domains (e.g. Government agency, bank systems) may invoke the ASP to authenticate the end-user and obtain the access tokens to access the protected resources.

Client apps invoke the ASP through an interface and interaction flows based on the widely supported OpenID Connect (OIDC) specifications.

The ASP can be federated – it may be run and operated independently from the Government NDI cluster, e.g. a financial institution may run an instance of ASP on its platform to serve the needs of its applications.  

This guide focuses on how the ASP may be federated and the integration required.  