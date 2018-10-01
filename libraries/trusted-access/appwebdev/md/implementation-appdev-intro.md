### Introduction
<br/>

This technical guide is meant for developers of Mobile Apps integrating with the NDI Authentication Service Provider (ASP), to perform user authentication with NDI.  The Mobile App may also leverage the NDI ASP as an OpenID Connect Provider, to obtain access tokens to access API/resources of a protected domain, which may be a Government or commercial entity such as a bank. 

#### ASP Overview
<br/>

A protected domain refers to a network of computerized systems run by an organization (e.g. Government agency, bank) to manage its digital assets (e.g. customer data, accounts and transactions).  Digital assets or resources in the protected domain are protected from unauthorized access by cybersecurity measures and access control policies implemented by the organization.

While traditionally protected domains are accessible only by internal applications, organizations are providing partners and apps access to their protected domains through open API.  To gain access to the protected domain, partners and app developers will have to deal with the organization’s authentication and authorization mechanism, which varies from domain to domain.  NDI aims to give app developers a standardized authentication and authorization approach, regardless of the protected domain they are accessing.

The ASP is essentially an OpenID Connect Provider (OP), the ASP API is defined based on the OpenID Connect specifications, in the form of a NDI ASP Profile.

Typically, an OP requires users to authenticate themselves using a user id and password.  With NDI, this is replaced with the user’s digital identity in a secure form factor and access to the mobile device hosting the form factor.

#### A New Way of Authentication for Mobile App Developers
<br/>

![appdev authentication](/assets/lib/trusted-access/appwebdev/img/appdevauthentication.png)
 
Your Mobile App interacts with the NDI App to create a seamless login experience for the user.  The integration with the NDI App is done by launching it with parameters through deep linking, similar to the OAuth flow on the Web but instead of going to different URLs it will between app to app.  Integrating with the NDI App simplifies the authentication task for your Mobile App as the NDI App handles most of the interactions with the ASP.

The interface diagram above depicts the interactions that take place during user authentication.  You need only focus on the interactions between your Mobile App and the NDI App (steps 2, 11) and ASP (step 12).

The following steps describe the interactions in detail:

- The Mobile App initiates a NDI login by instantiating an authentication instance with the NDI App.  This is done by making use of deep linking to the NDI App, passing on a callback URI which will be used by the NDI App to return the authorization code.

- The NDI App interacts with the ASP and requests the user to authenticate.

- Upon the user accepting the authentication request, the NDI App generates and sends the signed response to the ASP.  The ASP verifies the signed response – check the user certificate status and the chain of trust, then verify the signature of the signed response with the user certificate.  If all is well, the ASP returns a randomly generated authorization code to the NDI App.

- The NDI App invokes the callback URI of the Mobile App (using deep linking) to pass the authorization code back to the Mobile App.

- The Mobile App invokes the ASP token endpoint directly to obtain for the security tokens (ID token and access token) using the authorization code.

- The Mobile App uses the access token to access the API/resources of the target protected domain.

#### Proof Key for Code Exchange (PKCE)
<br/>

For some older mobile OS versions, it is possible for a malicious app to register itself as a handler for the custom scheme (used in the deep linking interactions depicted in the previous section) in addition to your Mobile App.  The malicious app will be able to intercept the authorization code returned by the NDI App, and use it to request for an access token from the ASP.

To mitigate this threat, it is mandatory to use Proof Key for Code Exchange (PKCE) during the authorization flow.  The PKCE specification requires additional parameters to the authorization and token exchange requests, in the form of a code challenge and code verifier respectively.   Refer to the RFC 7636 – Proof Key for Code Exchange by OAuth Public Clients for more details.