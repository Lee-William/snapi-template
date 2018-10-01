### Introduction 
<br/>

This technical guide is meant for developers of Web Applications (Web App accessed by users through desktop or mobile browsers) to integrate with the NDI Authentication Service Provider (ASP), to perform user authentication with NDI.  The Web App may also leverage the NDI ASP as an OpenID Connect Provider, to obtain access tokens to access API/resources of a protected domain, which may be a Government or commercial entity such as a bank. 

#### ASP Overview
<br/>

A protected domain refers to a computerized system (or a network of computerized systems) run by an organization (e.g. Government agency, bank) to manage its digital assets (e.g. customer data, accounts) and transactions.  Digital assets or resources in the protected domain are protected from unauthorized access with cybersecurity measures and access control policies implemented by the organization.

While traditionally protected domains are accessible only by internal applications, organizations are providing partners and apps access to their protected domains through open API.  In order to access the protected domain, partners and app developers will have to deal with the organization’s authentication and authorization mechanism, which varies from domain to domain.  NDI aims to give app developers a standardized authentication and authorization approach, regardless of the protected domain they are accessing.

The ASP is essentially an OpenID Connect Provider (OP), the ASP API is defined based on the OpenID Connect specifications, in the form of a NDI ASP Profile.

Typically, an OP requires users to authenticate themselves using a user id and password.  With NDI, this is replaced with the user’s digital identity in a secure form factor and access to the mobile device hosting the form factor. 

#### A New Way of Authentication
<br/>

![webdev authentication](/assets/lib/trusted-access/appwebdev/img/webdev_authn.png)

The authentication mechanism used by ASP differs from the traditional approach where the client app captures the end-user’s user credentials/secrets through a login screen and forwards the credentials/secrets to a backend user management system which verifies against a centralized password database.

The ASP authentication mechanism utilizes a distributed approach as follows:

##### Invocation Options

These are the options the Web App may use to initiate the authentication and authorization flow orchestrated by the ASP:

<ol type="a">
  <li>Redirection – instead of having its own login screen, the Web App (client app) invokes the authorization endpoint of the ASP through redirection, redirecting the end-user to the NDI Login Page.  The end-user enters his/her NDI id and click submit on the Login Page, which sends an authentication request consisting the NDI id to the ASP.</li>
  <li>Direct Invocation – the Web App captures the end-user’s NDI id using its own login screen and invokes the authorization endpoint of the ASP directly, passing the NDI id as a parameter of the request.  This is similar to the OpenID Connect Client Initiated Backchannel Authentication (CIBA) flow.</li>
  <li>QR Code – the Web App invokes the authorization 
endpoint of the ASP to request for a QR code which encodes the authentication challenge from the ASP.  It then displays the QR code on its login screen to let the user capture using the NDI App.  This is similar to the OpenID connect Client Initiated Backchannel Authentication (CIBA) flow.</li>
</ol>

##### Initiate the Authentication Challenge
<br/>

For Invocation Option (a) and (b)

- On receiving the authentication request consisting the NDI id, the ASP obtains the end-user’s form factor attributes from the NDI User Directory, based on his NDI id.  The form factor attributes consist of the end-user’s preferred form factor type, the form factor address (e.g. push notification registration token), etc.  In this case, the end-user’s preferred form factor is the NDI soft token.  The ASP then routes an authentication request (in the form of a signed challenge) via push notification to the NDI App on the end-user’s mobile device where the soft token form factor resides.

- The end-user receives the push notification on his mobile device, inspects the description (e.g. name of the client app and scope of access requested) and accepts the authentication request.  In accepting the authentication request, the end-user may have to enter a PIN (or use fingerprint biometric) to unlock the soft token (which safekeep the end-user’s private key).

- The soft token verifies the signature of the signed challenge to ensure it is from an authorised ASP, and generates a response based on the challenge material, signed with the end-user’s private key.  It then returns the signed response to the ASP via the callback URI specified in the challenge. 

For Invocation Option (c)

- On capturing the QR code, the NDI App decodes the signed challenge encoded in the QR code and displays the description (e.g. name of the client app and scope of access requested) for the user to accept the authentication request.  In accepting the authentication request, the end-user may have to enter a PIN (or use fingerprint biometric) to unlock the soft token (which safekeep the end-user’s private key).


For Invocation Option (a) and (b)

- On receiving the authentication request consisting the NDI id, the ASP obtains the end-user’s form factor attributes from the NDI User Directory, based on his NDI id.  The form factor attributes consist of the end-user’s preferred form factor type, the form factor address (e.g. push notification registration token), etc.  In this case, the end-user’s preferred form factor is the NDI soft token.  The ASP then routes an authentication request (in the form of a signed challenge) via push notification to the NDI App on the end-user’s mobile device where the soft token form factor resides.

- The end-user receives the push notification on his mobile device, inspects the description (e.g. name of the client app and scope of access requested) and accepts the authentication request.  In accepting the authentication request, the end-user may have to enter a PIN (or use fingerprint biometric) to unlock the soft token (which safekeep the end-user’s private key).

- The soft token verifies the signature of the signed challenge to ensure it is from an authorised ASP, and generates a response based on the challenge material, signed with the end-user’s private key.  It then returns the signed response to the ASP via the callback URI specified in the challenge. 

For Invocation Option (c)

- On capturing the QR code, the NDI App decodes the signed challenge encoded in the QR code and displays the description (e.g. name of the client app and scope of access requested) for the user to accept the authentication request.  In accepting the authentication request, the end-user may have to enter a PIN (or use fingerprint biometric) to unlock the soft token (which safekeep the end-user’s private key).

- The soft token verifies the signature of the signed challenge to ensure it is from an authorised ASP, and generates a response based on the challenge material, signed with the end-user’s private key.  It then returns the signed response, together with the end-user’s NDI id to the ASP via the callback URI specified in the challenge.

##### Verify the Signed Response

- The ASP verifies the signature of the signed response with the end-user’s digital certificate, after checking the integrity of digital certificate by verifying its certificate chain of trust, and the status of the digital certificate with the CA’s Online Certificate Status Protocol (OCSP) service.

- On successful user authentication, the ASP invokes the Authorization Service of the protected domain to obtain the access token for accessing resources of the protected domain.  The access token is a security token encapsulating assertions of the access granted, generated by the Authorization Service based on its access control policies and end-user consent.

For Invocation Option (a)

- The ASP sends an authorization code to the Web App by redirection to the Web App’s pre-registered redirect URI.  The Web App may then use the authorization code to exchange for the ID token and access token by invoking the ASP token endpoint.

- The Web App uses the access token to access the API/resources of the target protected domain.

For Invocation Option (b) and (c)

- The ASP returns the ID token and access token to the Web App by directly invoking the Web App’s pre-registered client notification endpoint.  

- The Web App uses the access token to access the API/resources of the target protected domain.
