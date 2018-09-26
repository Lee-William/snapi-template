### Introduction

<br> 

The Authentication Service Provider (ASP) is a key component of the NDI platform which performs authentication and authorization.  Client apps accessing resources (API, data) in protected domains (e.g. Government agency, bank systems) may invoke the ASP to authenticate the end-user and obtain the access tokens to access the protected resources.

Client apps invoke the ASP through an interface and interaction flows based on the widely supported OpenID Connect (OIDC) specifications.

The ASP can be federated – it may be run and operated independently from the Government NDI cluster, e.g. a financial institution may run an instance of ASP on its platform to serve the needs of its applications.  

This guide focuses on how the ASP may be federated and the integration required.  

#### Federated ASP Pack 

This is for organizations which have on-premises requirement.  Obtain and deploy pre-built ASP modules at the federated site.  The deployment of the pre-built ASP pack at the federated site is made relatively easy by support for Open Standards and commodity infrastructure.  The deployment steps are largely automated through deployment scripts.

##### Deployment Pre-requisites