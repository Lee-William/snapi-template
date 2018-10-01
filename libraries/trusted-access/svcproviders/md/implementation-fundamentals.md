### NDI Fundamentals
<br/>

#### ASP Integration Overview
<br/>

The Authentication Service Provider (ASP) is a key component of the NDI platform which performs authentication and authorization.  Client apps accessing resources (API, data) in protected domains (e.g. Government agency, bank systems) may invoke the ASP to authenticate the end-user and obtain the access tokens to access the protected resources.

Client apps invoke the ASP through an interface and interaction flows based on the widely supported OpenID Connect (OIDC) specifications.

The ASP can be federated – it may be run and operated independently from the Government NDI cluster, e.g. a financial institution may run an instance of ASP on its platform to serve the needs of its applications.  
This guide focuses on how the ASP may be federated and the integration required.  

#### ASP Operating Modes 
<br/>

The ASP may be operated in the 2 modes – as an OIDC Provider, or as a pure-play authenticator.  The operating modes will decide how relying parties (i.e. the client app) and the Authorization Server of the federated site interact with the ASP.  In both modes, the ASP is only responsible for authenticating the user and generates the ID token, it is the organization’s Authorization Server which determines whether the relying party and the user are authorized to access its protected resources and issues the access token accordingly. 

##### ASP as an OpenID Connect Provider(OP)
<br/>

![ASP as an OIDC Provider](/assets/lib/trusted-access/resowners/img/aspoidcprovider.png)

The ASP acts as an OIDC Provider, handling the OIDC flow with the relying party.  This operating mode is useful for organizations which are planning to expose their capabilities through API and may not have a OAuth 2.0/OIDC enabled Authorization Server.  During the OIDC flow, the ASP performs user authentication with the user’s NDI form factor, on successful authentication, it calls the organization’s Authorization Server to obtain an access token.  It returns the access token to the relying party which may then use it to access protected resources.

In this operating mode, the ASP integrates with the Authorization Server of the organization through the Domain Authorization Interface.

##### ASP as an Authenticator
<br/>

![ASP as an Authenticator](/assets/lib/trusted-access/resowners/img/aspauthenticator.png)

The ASP acts as an authenticator service which the Authorization Server calls to perform user authentication with the user’s NDI form factor.  This operation mode is applicable for organizations which are already offering OAuth 2.0 or OIDC based authorization to relying parties accessing their protected resources.  In this scenario, organizations typically use an Authorization Server (or IAM) module to handle the OAuth 2.0/OIDC flows with relying parties.  During the OAuth 2.0/OIDC flow, the Authorization Server module calls the ASP authentication API to perform user authentication with the user’s NDI form factor.  On successful user authentication, the Authorization Server generates and returns an access token to the relying party which it may use to access protected resources.

In this operating mode, the Authorization Server of the organization will integrate with the ASP through the ASP Authentication API.