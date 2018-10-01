National Digital Identity (NDI)

Relying Party (Web App)

Technical Reference

This technical reference describes how web-based relying parties (client
apps) integrates with the NDI Authentication Service Provider (ASP) for
authentication and authorization.

0.3b Draft (28-Sep-2018)

The content of this document is for your general information and is
subject to change without notice.

<table>
<thead>
<tr class="header">
<th>Date</th>
<th>Revision</th>
<th>By</th>
<th>Comments</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>28-Sep-2018</td>
<td>0.3b</td>
<td>Weng Kong Kwok</td>
<td><ul>
<li><p>Used finalized sandbox URLs</p></li>
</ul></td>
</tr>
<tr class="even">
<td>8-Aug-2018</td>
<td>0.3</td>
<td>Weng Kong Kwok</td>
<td><ul>
<li><p>Added Direct Invocation (OIDC CIBA) and QR Code Invocation flow</p></li>
</ul></td>
</tr>
<tr class="odd">
<td>31-Jul-2018</td>
<td>0.2</td>
<td>Weng Kong Kwok</td>
<td><ul>
<li><p>Updated enrolment and client registration app screens</p></li>
</ul></td>
</tr>
<tr class="even">
<td>15-Mar-2018</td>
<td>0.1</td>
<td>Weng Kong Kwok</td>
<td><ul>
<li><p>First draft</p></li>
</ul></td>
</tr>
<tr class="odd">
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</tbody>
</table>

Contents
========

[Introduction 4](#introduction)

[ASP Overview 4](#asp-overview)

[A New Way of Authentication 4](#a-new-way-of-authentication)

[Invocation Options 5](#invocation-options)

[Initiate the Authentication Challenge
5](#initiate-the-authentication-challenge)

[Verify the Signed Response 6](#verify-the-signed-response)

[Step by Step Guide 7](#step-by-step-guide)

[Configure Sandbox 7](#configure-sandbox)

[Create Test Digital Identity 7](#create-test-digital-identity)

[Try Out NDI Login 7](#try-out-ndi-login)

[Create Client Credentials 8](#create-client-credentials)

[Make the First Call 8](#make-the-first-call)

[Register Your Web App 9](#register-your-web-app)

[ASP API Reference 10](#asp-api-reference)

[Authorization Endpoint 10](#authorization-endpoint)

[Redirect URI 10](#redirect-uri)

[Redirection Invocation 11](#redirection-invocation)

[Direct Invocation 11](#direct-invocation)

[QR Code Invocation 12](#qr-code-invocation)

[Authorization API 12](#authorization-api)

[Authorization Endpoint (Redirection)
12](#authorization-endpoint-redirection)

[Authorization Endpoint (Direct Invocation)
14](#authorization-endpoint-direct-invocation)

[Token Endpoint 18](#token-endpoint)

[Token Exchange API 19](#token-exchange-api)

[Refresh Token API 21](#refresh-token-api)

[Discovery Document 23](#discovery-document)

[Security Tokens 25](#security-tokens)

[ID Token 25](#id-token)

[Access Token 26](#access-token)

[Refresh Token 26](#refresh-token)

[Safe Keeping of Security Tokens 26](#safe-keeping-of-security-tokens)

[Exception Handling 27](#exception-handling)

[General Error Handling 27](#general-error-handling)

[Authentication Error 27](#authentication-error)

[Client Error (HTTP 4xx) 27](#client-error-http-4xx)

[Server Error (HTTP 500) 27](#server-error-http-500)

[Timeout Error 27](#timeout-error)

[No Response 27](#no-response)

[Error Codes 27](#error-codes)

Introduction
============

> This technical guide is meant for developers of Web Applications (Web
> App accessed by users through desktop or mobile browsers) to integrate
> with the NDI Authentication Service Provider (ASP), to perform user
> authentication with NDI. The Web App may also leverage the NDI ASP as
> an OpenID Connect Provider, to obtain access tokens to access
> API/resources of a protected domain, which may be a Government or
> commercial entity such as a bank.

ASP Overview
------------

> A protected domain refers to a computerized system (or a network of
> computerized systems) run by an organization (e.g. Government agency,
> bank) to manage its digital assets (e.g. customer data, accounts) and
> transactions. Digital assets or resources in the protected domain are
> protected from unauthorized access with cybersecurity measures and
> access control policies implemented by the organization.
>
> While traditionally protected domains are accessible only by internal
> applications, organizations are providing partners and apps access to
> their protected domains through open API. In order to access the
> protected domain, partners and app developers will have to deal with
> the organization’s authentication and authorization mechanism, which
> varies from domain to domain. NDI aims to give app developers a
> standardized authentication and authorization approach, regardless of
> the protected domain they are accessing.
>
> The ASP is essentially an OpenID Connect Provider (OP), the ASP API is
> defined based on the OpenID Connect specifications, in the form of a
> NDI ASP Profile.
>
> Typically, an OP requires users to authenticate themselves using a
> user id and password. With NDI, this is replaced with the user’s
> digital identity in a secure form factor and access to the mobile
> device hosting the form factor.

A New Way of Authentication
---------------------------

> <embed src="media/image1.emf" style="width:5.7248in;height:3.49133in" />
>
> The authentication mechanism used by ASP differs from the traditional
> approach where the client app captures the end-user’s user
> credentials/secrets through a login screen and forwards the
> credentials/secrets to a backend user management system which verifies
> against a centralized password database.
>
> The ASP authentication mechanism utilizes a distributed approach as
> follows:

### Invocation Options

> These are the options the Web App may use to initiate the
> authentication and authorization flow orchestrated by the ASP:

1.  Redirection – instead of having its own login screen, the Web App
    (client app) invokes the authorization endpoint of the ASP through
    redirection, redirecting the end-user to the NDI Login Page. The
    end-user enters his/her NDI id and click submit on the Login Page,
    which sends an authentication request consisting the NDI id to the
    ASP.

2.  Direct Invocation – the Web App captures the end-user’s NDI id using
    its own login screen and invokes the authorization endpoint of the
    ASP directly, passing the NDI id as a parameter of the request. This
    is similar to the OpenID Connect Client Initiated Backchannel
    Authentication (CIBA) flow.

3.  QR Code – the Web App invokes the authorization endpoint of the ASP
    to request for a QR code which encodes the authentication challenge
    from the ASP. It then displays the QR code on its login screen to
    let the user capture using the NDI App. This is similar to the
    OpenID connect Client Initiated Backchannel Authentication (CIBA)
    flow.

### Initiate the Authentication Challenge

> For Invocation Option (a) and (b)

-   On receiving the authentication request consisting the NDI id, the
    ASP obtains the end-user’s form factor attributes from the NDI User
    Directory, based on his NDI id. The form factor attributes consist
    of the end-user’s preferred form factor type, the form factor
    address (e.g. push notification registration token), etc. In this
    case, the end-user’s preferred form factor is the NDI soft token.
    The ASP then routes an authentication request (in the form of a
    signed challenge) via push notification to the NDI App on the
    end-user’s mobile device where the soft token form factor resides.

-   The end-user receives the push notification on his mobile device,
    inspects the description (e.g. name of the client app and scope of
    access requested) and accepts the authentication request. In
    accepting the authentication request, the end-user may have to enter
    a PIN (or use fingerprint biometric) to unlock the soft token (which
    safekeep the end-user’s private key).

-   The soft token verifies the signature of the signed challenge to
    ensure it is from an authorised ASP, and generates a response based
    on the challenge material, signed with the end-user’s private key.
    It then returns the signed response to the ASP via the callback URI
    specified in the challenge.

> For Invocation Option (c)

-   On capturing the QR code, the NDI App decodes the signed challenge
    encoded in the QR code and displays the description (e.g. name of
    the client app and scope of access requested) for the user to accept
    the authentication request. In accepting the authentication request,
    the end-user may have to enter a PIN (or use fingerprint biometric)
    to unlock the soft token (which safekeep the end-user’s private
    key).

-   The soft token verifies the signature of the signed challenge to
    ensure it is from an authorised ASP, and generates a response based
    on the challenge material, signed with the end-user’s private key.
    It then returns the signed response, together with the end-user’s
    NDI id to the ASP via the callback URI specified in the challenge.

### Verify the Signed Response

-   The ASP verifies the signature of the signed response with the
    end-user’s digital certificate, after checking the integrity of
    digital certificate by verifying its certificate chain of trust, and
    the status of the digital certificate with the CA’s Online
    Certificate Status Protocol (OCSP) service.

-   On successful user authentication, the ASP invokes the Authorization
    Service of the protected domain to obtain the access token for
    accessing resources of the protected domain. The access token is a
    security token encapsulating assertions of the access granted,
    generated by the Authorization Service based on its access control
    policies and end-user consent.

> For Invocation Option (a)

-   The ASP sends an authorization code to the Web App by redirection to
    the Web App’s pre-registered redirect URI. The Web App may then use
    the authorization code to exchange for the ID token and access token
    by invoking the ASP token endpoint.

-   The Web App uses the access token to access the API/resources of the
    target protected domain.

> For Invocation Option (b) and (c)

-   The ASP returns the ID token and access token to the Web App by
    directly invoking the Web App’s pre-registered client notification
    endpoint.

-   The Web App uses the access token to access the API/resources of the
    target protected domain.

Step by Step Guide
==================

Configure Sandbox
-----------------

> The NDI Sandbox is a full-featured system (albeit much diminished in
> scale and resiliency compared to the actual system) showcasing the key
> use cases and flows of NDI. It serves as a playground for developers
> to experiment with the API to quickly gain an understanding of how NDI
> works. Follow these steps to start your exploration.

### Create Test Digital Identity

> The NDI Sandbox provides the DemoKeys app which let you enrol test
> users to NDI Sandbox and create digital identities for them. Download
> and install the DemoKeys app from https://sandbox.demo.ndi.gov.sg and
> follow the steps to create test user ids which you can use to try out
> the ASP API.

<img src="media/image2.png" style="width:6.92431in;height:2.5032in" />

### Try Out NDI Login

> You should use the Demo-WebApp application provided by the Sandbox to
> ensure the test users you created are working.

1.  Open browser on your mobile device to
    https://sandbox.demo.ndi.gov.sg/demowapp/home

2.  Click Try Out NDI Login, the Demo-WebApp screen appears, click NDI
    Login to launch the NDI Login screen:

    <img src="media/image3.png" style="width:3.04861in;height:2.18726in" />

3.  Enter the test user id and click Login.

4.  You should receive a push notification on your device informing you
    of an authentication request. Clicking on the notification opens the
    DemoKeys Form Factor screen to let you enter the PIN to unlock the
    digital key of the test user.

### Create Client Credentials

> You will need client credentials for your Web App to perform
> authentication and authorization with Sandbox ASP. You can create them
> through the Sandbox Client Registration process at
> <https://sandbox.demo.ndi.gov.sg/>clnreg:

<img src="media/image4.png" style="width:6.83333in;height:1.87431in" />

### Make the First Call

> After ensuring your test users are working, you are all set to make
> your first API call. Invoke the ASP authorization endpoint as follows:
>
> [https://sandbox.api.ndi.gov.sg/asp/api/v1/asp/auth?client\_id={your\_client\_id}&](https://sandbox.api.ndi.gov.sg/asp/api/v1/asp/auth?client_id=%7byour_client_id%7d&)
>
> response\_type=code&
>
> scope=openid&redirect\_uri={your\_redirect\_uri}&
>
> state={random\_state}&nonce={random\_nonce}
>
> Replace {your\_client\_id} with the client id assigned to your Web App
> by the Client Registration process
>
> {your\_redirect\_uri} with the URI to return the authorization code to
> your Web App, e.g. <https://www.ganymede.com/code>
>
> {random\_state} with a random alphanumeric string, e.g. 10af9431enf5
>
> {random\_nonce} with a random alphanumeric string, e.g. da5492bc0h
>
> On successful invocation, the NDI Login screen appears.
>
> You may now proceed to explore the other ASP API at the Developer
> Portal and download the Demo-WebApp sample code as a reference for
> integrating the ASP API into your Web App.

Register Your Web App
---------------------

> When you complete the integration of your Web App with NDI using the
> NDI Sandbox, you are ready to register your Web App at the Developer
> Portal to start the actual testing and deployment process. During the
> NDI Client App Registration process, you will be required to provide
> information about your organization, detail use case description of
> your Web App, and the expected TPS of authentication and other API
> calls. This information will be taken as inputs to the review process
> to grant the use of NDI to your Web App.
>
> Visit the Developer Portal for more information on NDI Client App
> Registration.

ASP API Reference
=================

Authorization Endpoint
----------------------

> Your Web App uses this endpoint to initiate the user authentication
> flow. There are 3 options the Web App may use to initiate the
> authentication flow:

-   Redirection – the Web App is to redirect the user agent to the
    authorization endpoint of the ASP, which typically returns the NDI
    Login page for the user to enter his NDI Id for authentication. The
    ASP then routes an authentication challenge to the user's form
    factor (e.g. the NDI soft token on his mobile device) via the
    appropriate protocols. In the case of NDI soft token, the
    authentication challenge is sent to the user via push notification
    to the NDI App on his mobile device, where the user enters his PIN
    to unlock the soft token to sign on the authentication challenge.
    The NDI App returns the signed response to the ASP which verifies
    the signature with the user's certificate. If all is well, the ASP
    returns a randomly generated authorization code to the Web App via
    HTTP redirect. The destination of the HTTP redirect is the
    redirect\_uri parameter that you have configured for your Web App
    during Client App Registration.

-   Direct Invocation – This is similar to the OpenID Connect
    Client-Initiated Backchannel Authentication (CIBA) flow. Instead of
    redirecting the user agent to the NDI Login Page, the Web App
    captures the end-user’s NDI id using its own login screen and
    directly invokes the authorization endpoint of the ASP, passing the
    NDI id as a parameter of the request. The ASP returns the token
    response consisting the ID token and access token to the Web App by
    directly invoking the Web App’s client notification endpoint.
    Alternatively, the Web App may poll the token endpoint to obtain the
    token response when it is available. To use this invocation option,
    your Web App must be able to perform mTLS with the ASP.

    <embed src="media/image5.emf" style="width:5.63611in;height:1.34722in" />

-   QR Code Invocation– This is similar to the OpenID Connect
    Client-Initiated Backchannel Authentication (CIBA) flow. The Web App
    directly invokes the authorization endpoint of the ASP to request
    for a QR code which encodes the authentication challenge from the
    ASP. It then displays the QR code on its login screen to let the
    end-user capture using the NDI App. On capturing the QR code, the
    NDI App decodes the authentication challenge encoded in the QR code,
    displays the description (e.g. name of the client app and scope of
    access requested) and let the user enters his PIN to unlock the soft
    token to sign on the authentication challenge. The NDI App sends the
    signed response to the ASP which verifies the signature with the
    user's certificate. If all is well, the ASP returns the token
    response consisting the ID token and access token to the Web App by
    directly invoking the Web App’s client notification endpoint.
    Alternatively, the Web App may poll the ASP token endpoint to obtain
    the token response when it is available. To use this invocation
    option, your Web App must be able to perform mTLS with the ASP.

### Redirect URI

> As part of the security model, if your Web App passed to the
> authorization endpoint a redirect\_uri that has not been configured
> during Client App Registration, the ASP will respond with an error.
> This is to protect against phishing of the ASP authorization endpoint
> which let a malicious party replaces the redirect\_uri with its own
> address to obtain the authorization code.
>
> To ensure the authorization code (and subsequently the ID token and
> access token) is returned to a server in a controlled environment
> instead of frontend code residing in the user’s desktop/mobile
> browser, the redirect\_uri must be a legit domain name under your
> control which can be whitelisted by the ASP.

### Redirection Invocation

> This is a typical redirection URL to redirect the user agent to the
> authorization endpoint:
>
> [https://sandbox.api.ndi.gov.sg/asp/api/v1/asp/auth?client\_id={your\_client\_id}&](https://sandbox.api.ndi.gov.sg/asp/api/v1/asp/auth?client_id=%7byour_client_id%7d&)
>
> response\_type=code&scope={req\_scope}&redirect\_uri={your\_redirect\_uri}&
>
> state={random\_state}&nonce={random\_nonce}

Replace {your\_client\_id} with the assigned client id of your Web App

{req\_scope} with the scope of access requested (e.g. “openid cpf”)

> {your\_redirect\_uri} with the URI for the ASP to return the
> authorization code to your Web App, e.g.
> “<https://www.ganymede.com/code>”
>
> {random\_state} with a random alphanumeric string, e.g. 10af9431enf5
>
> {random\_nonce} with a random alphanumeric string, e.g. da5492bc0h
>
> On successful invocation, the NDI Login screen appears.

### Direct Invocation

> Direct Invocation (DI) is supported by another authorization endpoint
> of the ASP. This is a typical direct invocation call to the DI
> authorization endpoint:
>
> POST /asp/api/v1/asp/di-auth HTTP/1.1
>
> Host: sandbox.api.ndi.gov.sg
>
> Content-Type: application/json
>
> {
>
> "client\_id" : {your\_client\_id},
>
> "client\_secret" : {your\_client\_secret},
>
> "scope" : {req\_scope},
>
> "client\_notification\_token" : {your\_notif\_token},
>
> "acr\_values" : "mod-mf",
>
> "login\_hint" : {ndi\_id\_hash},
>
> "binding\_message" : {your\_msg},
>
> "nonce" : {random\_nonce}
>
> }
>
> Replace {your\_client\_id} with the assigned client id of your Web App
>
> {your\_client\_secret} with the client secret generated for your Web
> App. Alternatively, this may be a token containing a digital signature
> token for certificate-based client authentication
>
> {req\_scope} with the scope of access requested, e.g. "openid cpf"
>
> {your\_notif\_token} with a unique id generated by your Web App which
> will be used by the ASP as a Bearer token to authenticate the request
> to send the token response to your Web App’s client notification
> endpoint
>
> {ndi\_id\_hash} with the hash value (SHA-256 hex) of the end-user’s
> NDI id
>
> {your\_msg} with the message to show the end-user on his form factor
> hosting device, e.g. to request for consent to access certain
> resources
>
> {random\_nonce} with a random alphanumeric string generated by your
> Web App

### QR Code Invocation

> QR Code Invocation is supported by the Direct Invocation (DI)
> authorization endpoint of the ASP. This is a typical invocation call
> to the DI authorization endpoint to request for a QR code encoded
> authentication challenge:
>
> POST /asp/api/v1/asp/di-auth HTTP/1.1
>
> Host: sandbox.api.ndi.gov.sg
>
> Content-Type: application/json
>
> {
>
> "client\_id" : {your\_client\_id},
>
> "client\_secret" : {your\_client\_secret},
>
> "scope" : {req\_scope},
>
> "client\_notification\_token" : {your\_notif\_token},
>
> "acr\_values" : "mod-mf",
>
> "display" : "qr-code",
>
> "binding\_message" : {your\_msg},
>
> "nonce" : {random\_nonce}
>
> }
>
> Replace {your\_client\_id} with the assigned client id of your Web App
>
> {your\_client\_secret} with the client secret generated for your Web
> App. This may be a token containing a digital signature token for
> certificate-based client authentication
>
> {req\_scope} with the scope of access requested, e.g. "openid cpf"
>
> {your\_notif\_token} with a unique id generated by your Web App which
> will be used by the ASP as a Bearer token to authenticate the request
> to send the token response to your Web App’s client notification
> endpoint
>
> {your\_msg} with the message to show the end-user on his form factor
> hosting device, e.g. to request for consent to access certain
> resources
>
> {random\_nonce} with a random alphanumeric string generated by your
> Web App
>
> On successful invocation, the QR code bitmap encoded in jpeg base64
> format is returned as the response.

Authorization API
-----------------

### Authorization Endpoint (Redirection)

> This endpoint supports the Redirection option. The API definition is
> as follows:
>
> **Request**
>
> **GET {basePath}/asp/auth**

<table>
<thead>
<tr class="header">
<th><strong>Required</strong></th>
<th></th>
<th></th>
<th></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><strong>Parameter</strong></td>
<td><strong>In</strong></td>
<td><strong>Type</strong></td>
<td><strong>Description</strong></td>
</tr>
<tr class="even">
<td>client_id</td>
<td>Query</td>
<td>String</td>
<td>The client id assigned to your Web App during Client App Registration</td>
</tr>
<tr class="odd">
<td>scope</td>
<td>Query</td>
<td>String</td>
<td>Space delimited and case-sensitive list of strings of OAuth 2.0 scope values, indicating the scope of access requested. Each scope value references a scope profile defined for your Web App during Client App Registration.</td>
</tr>
<tr class="even">
<td>nonce</td>
<td>Query</td>
<td>String</td>
<td>A random unique reference generated by the Web App, which will be included in the ID token returned by the ASP on successful user authentication. The Web App may use this to tie the ID token to a particular authenticated session, to mitigate replay attacks.</td>
</tr>
<tr class="odd">
<td><strong>Optional</strong></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td><strong>Parameter</strong></td>
<td><strong>In</strong></td>
<td><strong>Type</strong></td>
<td><strong>Description</strong></td>
</tr>
<tr class="odd">
<td>response_type</td>
<td>Query</td>
<td>String</td>
<td>Indicates the grant type flow to be used. This is defaulted to &quot;code&quot; for the Authorization Code flow, where an authorization code is used to exchange for the id token and access token.</td>
</tr>
<tr class="even">
<td>state</td>
<td>Query</td>
<td>String</td>
<td>The random string generated by the client to counter the Cross-Site Request Forgery (CSRF) threat. If specified, the ASP will include it as part of the redirect URL when returning the authorization code to the Web App's redirect URI. Your Web App is to match the value of the state returned with its copy to ensure the redirect is from the ASP.</td>
</tr>
</tbody>
</table>

> **Response**
>
> The ASP provides a 2-part response when its authorization endpoint is
> called by the Web App. The first response returns the NDI Login Page,
> the second response delivers the authorization code (or error code) to
> the Web App via its redirect URI.

<table>
<thead>
<tr class="header">
<th><strong>Success, the NDI Login page is returned for user authentication</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>HTTP/1.1 200 OK</p>
<p>Content-Type: text/html</p>
<p>Content: {HTML of the NDI Login page}</p></td>
</tr>
</tbody>
</table>

<table>
<thead>
<tr class="header">
<th><strong>On successful user authentication, the ASP returns the authorization code via HTTPS redirect to the Web App's redirect URI</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>HTTP/1.1 302 Found</p>
<p>Location: {redirect_uri}?code={auth_code}&amp;state={state_value}</p>
<p>Content-Length: 0</p>
<p>Access-Control-Allow-Origin: *</p>
<p>Access-Control-Allow-Headers:</p></td>
</tr>
</tbody>
</table>


<table>
<thead>
<tr class="header">
<th><strong>On error, the ASP returns the error code via HTTPS redirect to the Web App's redirect URI</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>HTTP/1.1 302 Found</p>
<p>Location: {redirect_uri}?error={error_code}&amp;state={state_value}</p>
<p>Content-Length: 0</p>
<p>Access-Control-Allow-Origin: *</p>
<p>Access-Control-Allow-Headers:</p></td>
</tr>
</tbody>
</table>

### Authorization Endpoint (Direct Invocation)

> The authorization endpoint supports the Direct Invocation and QR Code
> Invocation option. The API definition is as follows:
>
> **Request**
>
> **POST {basePath}/asp/di-auth**

<table>
<thead>
<tr class="header">
<th><strong>Required</strong></th>
<th></th>
<th></th>
<th></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><strong>Parameter</strong></td>
<td><strong>In</strong></td>
<td><strong>Type</strong></td>
<td><strong>Description</strong></td>
</tr>
<tr class="even">
<td>client_id</td>
<td>Body</td>
<td>String</td>
<td>The client id assigned to your Web App during Client App Registration</td>
</tr>
<tr class="odd">
<td>client_secret</td>
<td>Body</td>
<td>String</td>
<td><p>For the ASP to perform client authentication. This may be done by:</p>
<ul>
<li><p>Basic Authentication – this will be the client secret assigned to your Web App during Client App Registration, e.g. ab359ef2cd8b</p></li>
<li><p>Certificate-based Authentication – this will contain the digital signature generated by your Web App’s certificate registered during Client App Registration, e.g. aq03ca13mdaqAFzaQ…RmN234faG1Q=</p></li>
</ul></td>
</tr>
<tr class="even">
<td>scope</td>
<td>Body</td>
<td>String</td>
<td>Space delimited and case-sensitive list of strings of OAuth 2.0 scope values, indicating the scope of access requested. Each scope value references a scope profile defined for your Web App during Client App Registration</td>
</tr>
<tr class="odd">
<td>client_notification_token</td>
<td>Body</td>
<td>String</td>
<td><p>This parameter is required for non-block direct invocation (i.e. the authorization endpoint responds immediately, performs the user authentication subsequently and return the token response the Web App’s client notification endpoint). Set this to a unique id provided by your Web App which the ASP used as a Bearer token to authenticate the request sending the token response to the Web App’s client notification endpoint.</p>
<p>Omit this parameter for blocking direct invocation – the authorization endpoint will block until user authentication is completed and returns the ID token and access token as the response.</p></td>
</tr>
<tr class="even">
<td>acr_values</td>
<td>Body</td>
<td>String</td>
<td>The Authentication Method Reference value, set to “mod-mf”</td>
</tr>
<tr class="odd">
<td>login_hint</td>
<td>Body</td>
<td>String</td>
<td>Your Web App may capture the user's NDI Id using its UI instead of the NDI Login page. It passes the hash value (SHA-256 hex) of the NDI Id to the ASP through this parameter</td>
</tr>
<tr class="even">
<td>nonce</td>
<td>Body</td>
<td>String</td>
<td>A random unique reference generated by your Web App, which will be included in the ID token returned by the ASP on successful user authentication. The Web App may use this to tie the ID token to a particular authenticated session, to mitigate replay attacks.</td>
</tr>
<tr class="odd">
<td>display</td>
<td>Body</td>
<td>String</td>
<td>This parameter is required if you intend to initiate the QR Code based authentication. Set this to &quot;qr-code&quot; to request for QR code based authentication. A QR code which encodes the authentication challenge will be returned in the response, which the client app may display on its login screen for the user to capture using the NDI App.</td>
</tr>
<tr class="even">
<td><strong>Optional</strong></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td><strong>Parameter</strong></td>
<td><strong>In</strong></td>
<td><strong>Type</strong></td>
<td><strong>Description</strong></td>
</tr>
<tr class="even">
<td>binding_message</td>
<td>Body</td>
<td>String</td>
<td>This is a text provided by your Web App to be shown to the end-user on his form factor hosting device, e.g. a short description to request for user consent to access certain resources, or a random code displayed by the Web App which the end-user may match with that shown on his device to ensure that the request is from the Web App.</td>
</tr>
</tbody>
</table>

> **Response**
>
> The ASP provides a 2-part response when its DI authorization endpoint
> is called by the Web App. The first response returns an
> acknowledgement of the authentication initiation (for Direct
> Invocation) or the QR code (for QR Code Invocation). The second
> response delivers the token response to the Web App by invoking its
> client notification endpoint.
>
> *Direct Invocation (Non-Blocking)*

<table>
<thead>
<tr class="header">
<th><strong>Response 1, on success – acknowledgement that user authentication is initiated</strong></th>
<th></th>
<th></th>
<th></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>HTTP/1.1 200 OK</p>
<p>Content-Type: application/json</p>
<p>{</p>
<p>&quot;auth_req_id&quot; : &quot;21da8b9c-19d3-afec-038153ac5301&quot;</p>
<p>&quot;expires_in&quot; : 600</p>
<p>}</p></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td><strong>Parameter</strong></td>
<td><strong>In</strong></td>
<td><strong>Type</strong></td>
<td><strong>Description</strong></td>
</tr>
<tr class="odd">
<td>auth_req_id</td>
<td>Body</td>
<td>String</td>
<td>A unique id generated by the ASP to identify the instance of authentication initiated by the Web App. This value will be included in the token response to allow the Web App correlate its authentication request with the received tokens</td>
</tr>
<tr class="even">
<td>expires_in</td>
<td>Body</td>
<td>String</td>
<td>The lifespan of the auth_req_id in seconds since the authentication is initiated.</td>
</tr>
</tbody>
</table>

<table>
<thead>
<tr class="header">
<th><strong>Response 2, on successful user authentication – the ASP sends the token success response to Web App by directly invoking the Web App’s client notification endpoint through HTTPS POST</strong></th>
<th></th>
<th></th>
<th></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>POST /yournotifycallback HTTP/1.1</p>
<p>Host: yourwebapp.example.com</p>
<p>Authorization: Bearer 8dd47b9e-2c43-67de-139953fc4b0c</p>
<p>Content-Type: application/json</p>
<p>{</p>
<p>&quot;auth_req_id&quot; : &quot;21da8b9c-19d3-afec-038153ac5301&quot;,</p>
<p>&quot;acces_token&quot; : &quot;bc902d145fe6&quot;,</p>
<p>&quot;token_type&quot; : &quot;Bearer&quot;,</p>
<p>&quot;expires_in&quot; : 600,</p>
<p>&quot;id_token&quot; : &quot;eyJ0eXAiOiJKV1QiLCJhbGciOiJFUzI1NiIsImtpZCI6ImdhbnltZWRlIn0.eyJjbGllbnRfaWQiOiJjb2 1lb25zcHVycyIsImV4cCI6MTIzMjEzNDEyNCwiaWF0IjoyNDEyNDEyMTM3NX0.W_4ViMyL4VhbQRvfLYb2uXSaTmrDWmtd8SGH971ORR6shpWZRoMprzGPcR8VVkwKVYvl0H_8NWSaMudnsGxwFg&quot;</p></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td><strong>Parameter</strong></td>
<td><strong>In</strong></td>
<td><strong>Type</strong></td>
<td><strong>Description</strong></td>
</tr>
<tr class="odd">
<td>client_notification_token</td>
<td>Header (Authorization)</td>
<td>String</td>
<td>This is the client notification token provided by your Web App in the request to the DI authorization endpoint, it is used here as a Bearer token to allow your Web App carry out client authentication</td>
</tr>
<tr class="even">
<td>auth_req_id</td>
<td>Body</td>
<td>String</td>
<td>This is the same auth_req_id returned by the ASP in the acknowledgement to your Web App’s request to the DI authorization endpoint.</td>
</tr>
</tbody>
</table>

> Refer the Token Exchange API session for description of the other
> parameters returned in the token response.

<table>
<thead>
<tr class="header">
<th><strong>Response 2, on user authentication error – the ASP sends the token error response to Web App by directly invoking the Web App’s client notification endpoint through HTTPS POST</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>POST /yournotifycallback HTTP/1.1</p>
<p>Host: yourwebapp.example.com</p>
<p>Authorization: Bearer 8dd47b9e-2c43-67de-139953fc4b0c</p>
<p>Content-Type: application/json</p>
<p>{</p>
<p>&quot;auth_req_id&quot; : &quot;21da8b9c-19d3-afec-038153ac5301&quot;,</p>
<p>&quot;error&quot; : &quot;unauthorized_client&quot;,</p>
<p>&quot;error_description&quot; : &quot;Client is not allowed to use direct invocation&quot;</p>
<p>}</p></td>
</tr>
</tbody>
</table>

> *Direct Invocation (Blocking)*

<table>
<thead>
<tr class="header">
<th><strong>On successful user authentication – the ASP returns the access token as success response to Web App as in the following example</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>HTTP/1.1 200 OK</p>
<p>Content-Type: application/json</p>
<p>{</p>
<p>“acces_token” : “bc902d145fe6”,</p>
<p>“token_type” : “Bearer”,</p>
<p>“expires_in” : 600,</p>
<p>“id_token” : “eyJ0eXAiOiJKV1QiLCJhbGciOiJFUzI1NiIsImtpZCI6ImdhbnltZWRlIn0.eyJjbGllbnRfaWQiOiJjb2 1lb25zcHVycyIsImV4cCI6MTIzMjEzNDEyNCwiaWF0IjoyNDEyNDEyMTM3NX0.W_4ViMyL4VhbQRvfLYb2uXSaTmrDWmtd8SGH971ORR6shpWZRoMprzGPcR8VVkwKVYvl0H_8NWSaMudnsGxwFg”</p></td>
</tr>
</tbody>
</table>

> Refer the Token Exchange API session for description of the other
> parameters returned in the token response.

<table>
<thead>
<tr class="header">
<th><strong>On error – the ASP returns the error response to Web App as in the following example</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>POST /yournotifycallback HTTP/1.1</p>
<p>Host: yourwebapp.example.com</p>
<p>Authorization: Bearer 8dd47b9e-2c43-67de-139953fc4b0c</p>
<p>Content-Type: application/json</p>
<p>{</p>
<p>“error” : “unauthorized_client”,</p>
<p>“error_description” : “Client is not allowed to use direct invocation”</p>
<p>}</p></td>
</tr>
</tbody>
</table>

> *QR Code Invocation*

<table>
<thead>
<tr class="header">
<th><strong>Response 1, on success – the QR Code image (PNG format base64 encoded) embedding the authentication challenge is returned</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>HTTP/1.1 200 OK</p>
<p>Content-Type: application/json</p>
<p>{</p>
<p>&quot;auth_req_id&quot; : &quot;21da8b9c-19d3-afec-038153ac5301&quot;</p>
<p>&quot;expires_in&quot; : 600</p>
<p>&quot;qr_code&quot; :</p>
<p>&quot;iVBORw0KGgoAAAANSUhEUgAAASwAAAEsCAAAAABcFtGpAAAD/0lEQVR4nO3dQW7bMBRAwbrw/a+cbmUgH+GYotEWb3ZxIsl54IagRD1+nfA1fP74+U/u8vj5T9jvA+f8bxULFAsUCxQLFAsUCxQLFAsUCxQLPK8/bM3XpsnY9fOv7z+eXL/PcJrx8+k87HKBRhYoFigWKBYoFigWKBYoFigWKBYoFigWeI6/0ZkumibDein+ChsXa2SBYoFigWKBYoFigWKBYoFigWKBebpzwnBPqU59hlMev0+1kQWKBYoFigWKBYoFigWKBYoFigWKBT47NxyszO8+OQecNLJAsUCxQLFAsUCxQLFAsUCxQLFAsUCxwDyRPjxbXXnYcuVYtvF/NbJAsUCxQLFAsUCxQLFAsUCxQLFAscDL3PDERqgvDm/cM7nr/2pkgWKBYoFigWKBYoFigWKBYoFigWKBx0dvZh0eztQbcK8++f0bWaBYoFigWKBYoFigWKBYoFigWKBYYGmf0nH+pQtyf/F9qqPLiRpZoFigWKBYoFigWKBYoFigWKBYoFjgZdq0MnUb51m6ELhwzp01xNs2+ullkO8pFigWKBYoFigWKBYoFigWKBaYb5PcecYND125rP79eOzGulgjCxQLFAsUCxQLFAsUCxQLFAsUCxQLzDOlYTK2tHaGE7yd+SAv3+meLd0m+Z5igWKBYoFigWKBYoFigWKBYoFigcfK0h8vD37w1sjp2KXbOfELNbJAsUCxQLFAsUCxQLFAsUCxQLFAscDS1pvjOt3GAt6Jd9wvba+y8ZxgIwsUCxQLFAsUCxQLFAsUCxQLFAsUC+zdU4rHrhy8s8XLifXNtld5U7FAsUCxQLFAsUCxQLFAsUCxQLHA0l40vAaH70fQ+1pXLjX9zajnDe9VLFAsUCxQLFAsUCxQLFAsUCxQLPDk5/KuDr9jXdcBt17NN134opEFigWKBYoFigWKBYoFigWKBYoFigWWnjfcu8L3H9/1Or6Vc44XwH++kQWKBYoFigWKBYoFigWKBYoFigWKBeZ1wx2H9yldmSfyFHDhAo0sUCxQLFAsUCxQLFAsUCxQLFAsUCzwvP6wtYbIL6H//tC77h09sYbYyALFAsUCxQLFAsUCxQLFAsUCxQLFAsUCz/E3d81WceOe6Svowupt2rjnPcUCxQLFAsUCxQLFAsUCxQLFAsUC89zwBHyJyMI+q+N5Vjar1YMbWaBYoFigWKBYoFigWKBYoFigWKBY4PzccJhz7WzQs7H3ztrFhgs3skCxQLFAsUCxQLFAsUCxQLFAsUCxwNK7HflMeCK9fbWHM/8BxQLFAsUCxQLFAsUCxQLFAsUCxQJHHtHb2dRVT6nPJC6ddLhAIwsUCxQLFAsUCxQLFAsUCxQLFAv8AQBXemZIycxEAAAAAElFTkSuQmCC&quot;</p>
<p>}</p></td>
</tr>
</tbody>
</table>

<table>
<thead>
<tr class="header">
<th><strong>Response 2, on successful user authentication – the ASP sends the token success response to Web App by directly invoking the Web App’s client notification endpoint through HTTPS POST</strong></th>
<th></th>
<th></th>
<th></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>POST /yournotifycallback HTTP/1.1</p>
<p>Host: yourwebapp.example.com</p>
<p>Authorization: Bearer 8dd47b9e-2c43-67de-139953fc4b0c</p>
<p>Content-Type: application/json</p>
<p>{</p>
<p>“auth_req_id” : “21da8b9c-19d3-afec-038153ac5301”,</p>
<p>“acces_token” : “bc902d145fe6”,</p>
<p>“token_type” : “Bearer”,</p>
<p>“expires_in” : 600,</p>
<p>“id_token” : “eyJ0eXAiOiJKV1QiLCJhbGciOiJFUzI1NiIsImtpZCI6ImdhbnltZWRlIn0.eyJjbGllbnRfaWQiOiJjb2 1lb25zcHVycyIsImV4cCI6MTIzMjEzNDEyNCwiaWF0IjoyNDEyNDEyMTM3NX0.W_4ViMyL4VhbQRvfLYb2uXSaTmrDWmtd8SGH971ORR6shpWZRoMprzGPcR8VVkwKVYvl0H_8NWSaMudnsGxwFg”</p></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td><strong>Parameter</strong></td>
<td><strong>In</strong></td>
<td><strong>Type</strong></td>
<td><strong>Description</strong></td>
</tr>
<tr class="odd">
<td>client_notification_token</td>
<td>Header (Authorization)</td>
<td>String</td>
<td>This is the client notification token provided by your Web App in the request to the DI authorization endpoint, it is used here as a Bearer token to allow your Web App carry out client authentication</td>
</tr>
<tr class="even">
<td>auth_req_id</td>
<td>Body</td>
<td>String</td>
<td>This is the same auth_req_id returned by the ASP in the acknowledgement to your Web App’s request to the DI authorization endpoint.</td>
</tr>
</tbody>
</table>

> Refer the Token Exchange API session for description of the other
> parameters returned in the token response.

<table>
<thead>
<tr class="header">
<th><strong>Response 2, on user authentication error – the ASP sends the token error response to Web App by directly invoking the Web App’s client notification endpoint through HTTPS POST</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>POST /yournotifycallback HTTP/1.1</p>
<p>Host: yourwebapp.example.com</p>
<p>Authorization: Bearer 8dd47b9e-2c43-67de-139953fc4b0c</p>
<p>Content-Type: application/json</p>
<p>{</p>
<p>“error” : “unauthorized_client”,</p>
<p>“error_description” : “Client is not allowed to use direct invocation”</p>
<p>}</p></td>
</tr>
</tbody>
</table>

> *General Error Responses*

<table>
<thead>
<tr class="header">
<th><strong>Client Error, invalid client id</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>HTTP/1.1 400 Bad Request</p>
<p>Content-Type: application/json</p>
<p>{</p>
<p>&quot;error&quot; : &quot;invalid_client&quot;</p>
<p>}</p></td>
</tr>
</tbody>
</table>

<table>
<thead>
<tr class="header">
<th><strong>Client Error, invalid endpoint URL</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>HTTP/1.1 404 Not Found</td>
</tr>
</tbody>
</table>

<table>
<thead>
<tr class="header">
<th><strong>Server Error, server-side errors encountered</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>HTTP/1.1 500 Internal Server Error</p>
<p>Content-Type: application/json</p>
<p>{</p>
<p>&quot;error&quot; : {error_message}</p>
<p>}</p></td>
</tr>
</tbody>
</table>

Token Endpoint
--------------

> The token endpoint consists of the following API:

-   Token Exchange API – this is an API where your Web App can use the
    authorization code obtained from the authorization endpoint to
    exchange for the security tokens, namely the ID token and access
    token. The ID token is a JSON Web Token (JWT) containing information
    about the identity of the user authenticated. The access token is a
    security token which your Web App use to access API/resources of a
    protected domain (e.g. Government or commercial entity such as the
    Public Housing Agency or a bank). The access token is issued by the
    Authorization server of the protected domain and may be in the form
    of a JWT or a randomly generated reference. Each access token comes
    with an expiry date, once expired, your Web App will have to
    re-authenticate the user to obtain a new access token, or
    alternatively, if the refresh token is provided, your Web App may
    use the refresh token to obtain a new access token without the need
    to re-authenticate the user.

-   Refresh Token API – this is an API which supports the Refresh Token
    flow. If your Web App is granted the refresh token privilege during
    Client App Registration, your Web App will be issued with a refresh
    token by the Token Exchange API, in addition to the ID token and
    access token. Your Web App can use the refresh token subsequently to
    obtain a new access token for the user without the need for the user
    to perform user authentication again. Refresh tokens have expiry
    too, but usually much long-lived than access tokens.

-   Token Response Polling API – this is an API which the Web App may
    call to poll for the token response (consisting the ID token and
    access token) at certain intervals, after initiating a user
    authentication by Direct Invocation or QR Code Invocation to the ASP
    authorization endpoint.

### Token Exchange API

> The Token Exchange API is as follows:
>
> **Request**
>
> **POST {basePath}/asp/token**

<table>
<thead>
<tr class="header">
<th><strong>Required</strong></th>
<th></th>
<th></th>
<th></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><strong>Parameter</strong></td>
<td><strong>In</strong></td>
<td><strong>Type</strong></td>
<td><strong>Description</strong></td>
</tr>
<tr class="even">
<td>code</td>
<td>Body</td>
<td>String</td>
<td>The authorization code obtained from the authorization endpoint.</td>
</tr>
<tr class="odd">
<td>client_id</td>
<td>Body</td>
<td>String</td>
<td>The client id assigned to your Web App during Client App Registration</td>
</tr>
<tr class="even">
<td>client_secret</td>
<td>Body</td>
<td>String</td>
<td>The client secret that you obtained for your Web App during Client App Registration. This parameter may be used to hold a digital signature for certificate-based client authentication</td>
</tr>
<tr class="odd">
<td>redirect_uri</td>
<td>Body</td>
<td>String</td>
<td>The redirect URI of your Web App</td>
</tr>
<tr class="even">
<td>grant_type</td>
<td>Body</td>
<td>String</td>
<td>Set to authorization_code, as defined in OAuth 2.0 specifications.</td>
</tr>
</tbody>
</table>

> **Response**

<table>
<thead>
<tr class="header">
<th><strong>Success, the ID token and access token are returned</strong></th>
<th></th>
<th></th>
<th></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>HTTP/1.1 200 OK</p>
<p>Content-Type: application/json</p>
<p>{</p>
<p>&quot;access_token&quot; : &quot;95c06f5a-26d4-11e8-b467-0ed5f89f718b&quot;</p>
<p>&quot;id_token&quot; : &quot;eyJ0eXAiOiJKV1QiLCJhbGciOiJFUzI1NiIsImtpZCI6ImdhbnltZWRlIn0.eyJjbGllbnRfaWQiOiJjb2 1lb25zcHVycyIsImV4cCI6MTIzMjEzNDEyNCwiaWF0IjoyNDEyNDEyMTM3NX0.W_4ViMyL4VhbQRvfLYb2uXSaTmrDWmtd8SGH971ORR6shpWZRoMprzGPcR8VVkwKVYvl0H_8NWSaMudnsGxwFg&quot;</p>
<p>&quot;expires_in&quot; : 600</p>
<p>&quot;token_type&quot; : &quot;Bearer&quot;</p>
<p>“refresh_token” : “24cd99ba-35da-28e4-b76e-4ef5e80f700f”</p>
<p>}</p></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td><strong>Parameter</strong></td>
<td><strong>In</strong></td>
<td><strong>Type</strong></td>
<td><strong>Description</strong></td>
</tr>
<tr class="odd">
<td>access_token</td>
<td>Body</td>
<td>String</td>
<td>The access token which can be used to access API and resources of your target protected domain. The access token may be in the form of a JWT or reference string, depending on the Authorization Server of the protected domain.</td>
</tr>
<tr class="even">
<td>id_token</td>
<td>Body</td>
<td>String</td>
<td>The ID token issued by ASP, in the form of a JWT containing identity info about the user authenticated with NDI-approved form factor. Your Web App will have to decode the JWT to inspect the claims in the ID token.</td>
</tr>
<tr class="odd">
<td>expires_in</td>
<td>Body</td>
<td>String</td>
<td>The lifetime (in seconds) of the access token. For example, the value &quot;600&quot; denotes the access token will expire in 10 mins from the time the response was generated.</td>
</tr>
<tr class="even">
<td>token_type</td>
<td>Body</td>
<td>String</td>
<td>The type of access token returned, which is always &quot;Bearer&quot;.</td>
</tr>
<tr class="odd">
<td>refresh_token</td>
<td>Body</td>
<td>String</td>
<td>The refresh token (if any, depending on the profile defined for the client app during Client App Registration) which the client app can use to refresh the access token.</td>
</tr>
</tbody>
</table>

<table>
<thead>
<tr class="header">
<th><strong>Client Error, invalid request (e.g. missing parameters)</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>HTTP/1.1 400 Bad Request</p>
<p>Content-Type: application/json</p>
<p>{</p>
<p>&quot;error&quot; : {error_message}</p>
<p>}</p></td>
</tr>
</tbody>
</table>

<table>
<thead>
<tr class="header">
<th><strong>Client Error, invalid endpoint URL</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>HTTP/1.1 404 Not Found</td>
</tr>
</tbody>
</table>

<table>
<thead>
<tr class="header">
<th><strong>Server Error, server-side errors encountered</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>HTTP/1.1 500 Internal Server Error</p>
<p>Content-Type: application/json</p>
<p>{</p>
<p>&quot;error&quot; : {error_message}</p>
<p>}</p></td>
</tr>
</tbody>
</table>

### Refresh Token API

> The Refresh Token API let a client app obtain a new access token
> without going through user authentication. The client app must supply
> the refresh token it obtained from the Token Exchange API to the
> Refresh Token request as follows:
>
> **Request**
>
> **POST {basePath}/asp/token**

<table>
<thead>
<tr class="header">
<th><strong>Required</strong></th>
<th></th>
<th></th>
<th></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><strong>Parameter</strong></td>
<td><strong>In</strong></td>
<td><strong>Type</strong></td>
<td><strong>Description</strong></td>
</tr>
<tr class="even">
<td>client_id</td>
<td>Body</td>
<td>String</td>
<td>The client id assigned to your client app during Client App Registration</td>
</tr>
<tr class="odd">
<td>client_secret</td>
<td>Body</td>
<td>String</td>
<td>The client secret that you obtained for your client app during Client App Registration</td>
</tr>
<tr class="even">
<td>grant_type</td>
<td>Body</td>
<td>String</td>
<td>Set to “refresh_token”, as defined in OAuth 2.0 specifications</td>
</tr>
<tr class="odd">
<td>refresh_token</td>
<td>Body</td>
<td>String</td>
<td>The refresh token obtained from the token endpoint (Token Exchange API)</td>
</tr>
<tr class="even">
<td><strong>Optional</strong></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td><strong>Parameter</strong></td>
<td><strong>In</strong></td>
<td><strong>Type</strong></td>
<td><strong>Description</strong></td>
</tr>
<tr class="even">
<td>scope</td>
<td>Body</td>
<td>String</td>
<td>Space delimited and case-sensitive list of strings of OAuth 2.0 scope values, indicating the scope of access requested. Each scope value references a scope profile defined for your client app during Client App Registration. If not provided, default to &quot;openid&quot;</td>
</tr>
</tbody>
</table>

> **Response**

<table>
<thead>
<tr class="header">
<th><strong>Success, the ID token and access token are returned</strong></th>
<th></th>
<th></th>
<th></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>HTTP/1.1 200 OK</p>
<p>Content-Type: application/json</p>
<p>{</p>
<p>&quot;access_token&quot; : &quot;95c06f5a-26d4-11e8-b467-0ed5f89f718b&quot;</p>
<p>&quot;token_type&quot; : &quot;Bearer&quot;</p>
<p>&quot;expires_in&quot; : 600</p>
<p>“refresh_token” : “77a906e51-94d4-26e8-cd6f-aed2fb9fa38e”</p>
<p>}</p></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td><strong>Parameter</strong></td>
<td><strong>In</strong></td>
<td><strong>Type</strong></td>
<td><strong>Description</strong></td>
</tr>
<tr class="odd">
<td>access_token</td>
<td>Body</td>
<td>String</td>
<td>The access token which can be used to access API and resources of your target protected domain. The access token may be in the form of a JWT or reference string, depending on the Authorization Server of the protected domain.</td>
</tr>
<tr class="even">
<td>expires_in</td>
<td>Body</td>
<td>String</td>
<td>The lifetime (in seconds) of the access token. For example, the value &quot;600&quot; denotes the access token will expire in 10 mins from the time the response was generated.</td>
</tr>
<tr class="odd">
<td>token_type</td>
<td>Body</td>
<td>String</td>
<td>The type of access token returned, which is always &quot;Bearer&quot;.</td>
</tr>
<tr class="even">
<td>refresh_token</td>
<td>Body</td>
<td>String</td>
<td>The new refresh token (if any) which the client app can use to refresh the access token.</td>
</tr>
</tbody>
</table>

<table>
<thead>
<tr class="header">
<th><strong>Client Error, invalid request (e.g. missing parameters)</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>HTTP/1.1 400 Bad Request</p>
<p>Content-Type: application/json</p>
<p>{</p>
<p>&quot;error&quot; : {error_message}</p>
<p>}</p></td>
</tr>
</tbody>
</table>

<table>
<thead>
<tr class="header">
<th><strong>Client Error, invalid endpoint URL</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>HTTP/1.1 404 Not Found</td>
</tr>
</tbody>
</table>

<table>
<thead>
<tr class="header">
<th><strong>Server Error, server-side errors encountered</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>HTTP/1.1 500 Internal Server Error</p>
<p>Content-Type: application/json</p>
<p>{</p>
<p>&quot;error&quot; : {error_message}</p>
<p>}</p></td>
</tr>
</tbody>
</table>

### Token Response Polling API

> The Token Response Polling API is as follows:
>
> **Request**
>
> **POST {basePath}/asp/token**

<table>
<thead>
<tr class="header">
<th><strong>Required</strong></th>
<th></th>
<th></th>
<th></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><strong>Parameter</strong></td>
<td><strong>In</strong></td>
<td><strong>Type</strong></td>
<td><strong>Description</strong></td>
</tr>
<tr class="even">
<td>client_id</td>
<td>Body</td>
<td>String</td>
<td>The client id assigned to your client app during Client App Registration</td>
</tr>
<tr class="odd">
<td>client_secret</td>
<td>Body</td>
<td>String</td>
<td>The client secret that you obtained for your client app during Client App Registration</td>
</tr>
<tr class="even">
<td>grant_type</td>
<td>Body</td>
<td>String</td>
<td>Set to “direct_invocation_request”</td>
</tr>
<tr class="odd">
<td>auth_req_id</td>
<td>Body</td>
<td>String</td>
<td>The unique id to identify the authentication request made by the Web App</td>
</tr>
</tbody>
</table>

> **Response**

<table>
<thead>
<tr class="header">
<th><strong>Success – the token response is available, the ID token and access token are returned</strong></th>
<th></th>
<th></th>
<th></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>HTTP/1.1 200 OK</p>
<p>Content-Type: application/json</p>
<p>{</p>
<p>&quot;access_token&quot; : &quot;95c06f5a-26d4-11e8-b467-0ed5f89f718b&quot;</p>
<p>&quot;id_token&quot; : &quot;eyJ0eXAiOiJKV1QiLCJhbGciOiJFUzI1NiIsImtpZCI6ImdhbnltZWRlIn0.eyJjbGllbnRfaWQiOiJjb2 1lb25zcHVycyIsImV4cCI6MTIzMjEzNDEyNCwiaWF0IjoyNDEyNDEyMTM3NX0.W_4ViMyL4VhbQRvfLYb2uXSaTmrDWmtd8SGH971ORR6shpWZRoMprzGPcR8VVkwKVYvl0H_8NWSaMudnsGxwFg&quot;</p>
<p>&quot;expires_in&quot; : 600</p>
<p>&quot;token_type&quot; : &quot;Bearer&quot;</p>
<p>“refresh_token” : “24cd99ba-35da-28e4-b76e-4ef5e80f700f”</p>
<p>}</p></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td><strong>Parameter</strong></td>
<td><strong>In</strong></td>
<td><strong>Type</strong></td>
<td><strong>Description</strong></td>
</tr>
<tr class="odd">
<td>access_token</td>
<td>Body</td>
<td>String</td>
<td>The access token which can be used to access API and resources of your target protected domain. The access token may be in the form of a JWT or reference string, depending on the Authorization Server of the protected domain.</td>
</tr>
<tr class="even">
<td>id_token</td>
<td>Body</td>
<td>String</td>
<td>The ID token issued by ASP, in the form of a JWT containing identity info about the user authenticated with NDI-approved form factor. Your Web App will have to decode the JWT to inspect the claims in the ID token.</td>
</tr>
<tr class="odd">
<td>expires_in</td>
<td>Body</td>
<td>String</td>
<td>The lifetime (in seconds) of the access token. For example, the value &quot;600&quot; denotes the access token will expire in 10 mins from the time the response was generated.</td>
</tr>
<tr class="even">
<td>token_type</td>
<td>Body</td>
<td>String</td>
<td>The type of access token returned, which is always &quot;Bearer&quot;.</td>
</tr>
<tr class="odd">
<td>refresh_token</td>
<td>Body</td>
<td>String</td>
<td>The refresh token (if any, depending on the profile defined for the client app during Client App Registration) which the client app can use to refresh the access token.</td>
</tr>
</tbody>
</table>

<table>
<thead>
<tr class="header">
<th><strong>Error – Token Error Response, e.g. invalid authentication request id</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>HTTP/1.1 400 Bad Request</p>
<p>Content-Type: application/json</p>
<p>{</p>
<p>&quot;error&quot; : {error_code}</p>
<p>}</p></td>
</tr>
</tbody>
</table>

<table>
<thead>
<tr class="header">
<th><strong>Error – Pending Token Response, token response is not available yet (e.g. user has not completed authentication), the Web App should continue to poll the token endpoint</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>HTTP/1.1 400 Bad Request</p>
<p>Content-Type: application/json</p>
<p>{</p>
<p>&quot;error&quot; : “unknown_auth_req_id”</p>
<p>}</p></td>
</tr>
</tbody>
</table>

Discovery Document
==================

> The OpenID Connect specifications involve the use of multiple
> endpoints for user authentication, and the requests for resources such
> as the security tokens and the public keys to verify these tokens. To
> simplify the implementation and discovery of these endpoints and
> resources, OpenID Connect provides for the use of a Discovery document
> – in JSON format, and downloadable from a well-known location – which
> describes the OpenID Connect Provider’s configuration and supported
> features.
>
> You will be able to download the ASP Discovery document from the
> following location:
>
> **GET {basePath}/.well-known/openid-configuration**
>
> Your Web App should download the ASP Discovery document periodically
> (it is safe to download the document on a daily basis) to keep up to
> date of the ASP’s configuration, as the ASP endpoints may change from
> time to time (e.g. from /v1 to /v2), and the signing keys are also
> refreshed regularly.
>
> The following shows a sample of the ASP discovery document:

<table>
<tbody>
<tr class="odd">
<td><p>{</p>
<p>“issuer” : “https://zeta1.asp.ndi.gov.sg”,</p>
<p>“authorization_endpoint” : “https://wog.ndi.gov.sg/v1/asp/auth”,</p>
<p>“token_endpoint” : “https://wog.ndi.gov.sg/v1/asp/token”,</p>
<p>“jwks_uri” : “https://wog.ndi.gov.sg/v1/certs”,</p>
<p>“response_types_supported” : [</p>
<p>“code”</p>
<p>],</p>
<p>“subject_types_supported” : [</p>
<p>“public”</p>
<p>],</p>
<p>“id_token_signing_algo_values_supported” : [</p>
<p>“ES256”,</p>
<p>“RS256”</p>
<p>],</p>
<p>“scope_supported” : [</p>
<p>“opened”</p>
<p>],</p>
<p>“token_endpoint_auth_methods_supported” : [</p>
<p>“client_secret_post”</p>
<p>],</p>
<p>“claim_supported” : [</p>
<p>“aud”,</p>
<p>“exp”,</p>
<p>“iat”,</p>
<blockquote>
<p>“iss”,</p>
</blockquote>
<p>“sub”</p>
<p>],</p>
<p>“code_challenge_methods_supported” : [</p>
<p>“plain”</p>
<p>“S256”</p>
<p>]</p>
<p>}</p></td>
</tr>
</tbody>
</table>

