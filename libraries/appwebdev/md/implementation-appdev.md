# A New Way of Authentication for App Developers

![appdev authentication](\assets\lib\appwebdev\img\appdevauthentication.png)
 
Your Mobile App interacts with the NDI App to create a seamless login experience for the user.  The integration with the NDI App is done by launching it with parameters through deep linking, similar to the OAuth flow on the Web but instead of going to different URLs it will between app to app.  Integrating with the NDI App simplifies the authentication task for your Mobile App as the NDI App handles most of the interactions with the ASP.

The interface diagram above depicts the interactions that take place during user authentication.  You need only focus on the interactions between your Mobile App and the NDI App (steps 2, 11) and ASP (step 12).

The following steps describe the interactions in detail:
- The Mobile App initiates a NDI login by instantiating an authentication instance with the NDI App.  This is done by making use of deep linking to the NDI App, passing on a callback URI which will be used by the NDI App to return the authorization code.
- The NDI App interacts with the ASP and requests the user to authenticate.
- Upon the user accepting the authentication request, the NDI App generates and sends the signed response to the ASP.  The ASP verifies the signed response – check the user certificate status and the chain of trust, then verify the signature of the signed response with the user certificate.  If all is well, the ASP returns a randomly generated authorization code to the NDI App.
-	The NDI App invokes the callback URI of the Mobile App (using deep linking) to pass the authorization code back to the Mobile App.
-	The Mobile App invokes the ASP token endpoint directly to obtain for the security tokens (ID token and access token) using the authorization code.
-	The Mobile App uses the access token to access the API/resources of the target protected domain.

## Proof Key for Code Exchange (PKCE)
For some older mobile OS versions, it is possible for a malicious app to register itself as a handler for the custom scheme (used in the deep linking interactions depicted in the previous section) in addition to your Mobile App.  The malicious app will be able to intercept the authorization code returned by the NDI App, and use it to request for an access token from the ASP. 
To mitigate this threat, it is mandatory to use Proof Key for Code Exchange (PKCE) during the authorization flow.  The PKCE specification requires additional parameters to the authorization and token exchange requests, in the form of a code challenge and code verifier respectively.   Refer to the RFC 7636 – Proof Key for Code Exchange by OAuth Public Clients for more details.

# Step by Step Guide
## Configure Sandbox

The NDI Sandbox is a full-featured system (albeit much diminished in scale and resiliency compared to the actual system) showcasing the key use cases and flows of NDI.  It serves as a playground for developers to experiment with the API to quickly gain an understanding of how NDI works.  Follow these steps to start your exploration.  

### Create Test Digital Identity

The NDI Sandbox provides the DemoKeys app which let you enrol test users to NDI Sandbox and create digital identities for them.  Download and install the DemoKeys app from https://sandbox.ndi.io and follow the steps to create test user ids which you can use to try out the ASP API.
 
![createtestidentity](\assets\lib\appwebdev\img\createtestidentity.png)


Try Out NDI Login
You should use the Demo-WebApp application provided by the Sandbox to ensure the test users you created are working.
1.	Open browser on your mobile device to https://sandbox.ndi.io/demo/login
2.	Click Try Out NDI Login, the Demo-WebApp screen appears, click NDI Login to launch the NDI Login screen:
 
3.	Enter the test user id and click Login.
4.	You should receive a push notification on your device informing you of an authentication request. Clicking on the notification opens the DemoKeys Form Factor screen to let you enter the PIN to unlock the digital key of the test user.
Create Client Credentials
You will need client credentials for your Mobile App to perform authentication and authorization with Sandbox ASP.  You can create them through the Sandbox Client Registration process at https://sandbox.ndi.io/clnreg:
 
Make the First Call
After ensuring your test users are working, you are all set to make your first API call.  Invoke the ASP authorization endpoint as follows:
https://sandbox.ndi.sg/api/v1/asp/auth?client_id={your_client_id}&response_type=code&
scope=openid&redirect_uri={your_redirect_uri}&
state={random_state}&nonce={random_nonce}
Replace	{your_client_id} with the client id assigned to your Mobile App by the Client Registration process
{your_redirect_uri} with the URI to return the authorization code to your Mobile App, e.g. https://www.ganymede.com/code
{random_state} with a random alphanumeric string, e.g. 10af9431enf5
{random_nonce} with a random alphanumeric string, e.g. da5492bc0h
On successful invocation, the NDI Login screen appears.
You may now proceed to explore and try out the other ASP API at the Developer Portal and download the Demo-WebApp sample code as a reference for integrating the ASP API into your Mobile App. 
Register Your Mobile App
When you complete the integration of your Mobile App with NDI using the NDI Sandbox, you are ready to register your Mobile App at the Developer Portal to start the actual testing and deployment process.  During the NDI Client App Registration process, you will be required to provide information about your organization, detail use case description of your Mobile App, and the expected TPS of authentication and other API calls.  This information will be taken as inputs to the review process to grant the use of NDI to your Mobile App.
Visit the Developer Portal for more information on NDI Client App Registration. 
Integration with the NDI App
You can initiate authentication by launching the NDI App with the required parameters. This will either be done with Universal Links (iOS)/ App Links (Android) or custom URI schemes.
iOS (9 and above only)
1.	You need to setup universal links.  Custom URI schemes will not be supported for iOS to provide better security.  You can refer to https://developer.apple.com/library/archive/documentation/General/Conceptual/AppSearch/UniversalLinks.html.  Alternatively, if you wish to use a third party solution for deep linking such as Branch and Firebase Dynamic Links, skip to step 4.
2.	First, you need to create an association from a website you own and your app.
For example, you can host and serve it as application/json at 
https://example.com/apple-app-site-association. You would need the app ID prefix/team ID and your bundle ID for appID. It should look something like this:
[{
  "applinks": {
    apps": [],
details": [{
  "appID": "92W9FM4VJN.com.example.app",
      "paths": [ "*" ]
    }]
  }
}]
3.	You also need to enable the associated domains capability in XCode.  You can do this by going to the Capabilities tab in XCode, turn it on and add an entry with your website with the prefix of applinks:
For example, applinks:example.com.
4.	If you are not using a third party solution go to the next step. Otherwise please ensure that you enable universal links.
5.	Then you need a universal link that the NDI app can return the authorization code to. For example, if your link is https://example.com/auth then the NDI app will return the authorization code by launching your app with https://example.com/auth?state={state}&code={code}.
6.	When the NDI app launches your app, you would need to retrieve and handle the corresponding data. In particular, you should add application:continueUserActivity:restorationHandler: in your Application Delegate.
7.	You can then initiate authentication by opening the NDI App’s universal link with openURL:
https://sandbox.ndi.io/auth?client_id={client_id}&scope={scope}
&redirect_uri={redirect_uri}&nonce={nonce}&state={state}
&code_challenge={challenge}&code_challenge_method={challenge_method}
Paremeters	Remarks
client_id (required)	The client ID for your app
redirect_uri	The universal link that the NDI App would return the authorization code to
nonce (required)	A one-time randomly generated unique string which will be included in the ID token as a means to prevent replay attacks
state	A one-time randomly generated unique string which will be returned along with the authorization code as a means to prevent CSRF and can also be used to maintain state
code_challenge_method (required)	The method used for transforming the code verifier. The only supported values are "plain" or "S256"
code_challenge (required)	The code challenge derived from the code verifier using the transformation specified by the code challenge method to enforce PKCE.

8.	The NDI App will then make the relevant requests to the ASP to initiate the user authentication. On completion of user authentication, the NDI App will call your redirect URI accordingly.  For example, suppose your redirect URI is https://example.com/redirect:
•	User authentication succeeded, your Mobile App will be called with the authorization code as follows: https://example.com/redirect?state={state}&code={code}
•	User authentication failed, your Mobile App will be called with an error as follows: https://example.com/redirect?error=access_denied&error_reason=user_denied&error_description=Permissions+error
9.	With the received authorization code, your Mobile App can now call the ASP token endpoint to obtain the ID token and access token accordingly.
Android
1.	You need to setup a custom URI scheme and app links for your app. You can refer to https://developer.android.com/training/app-links/.  Alternatively, if you wish to use a third party solution for deep linking such as Branch and Firebase Dynamic Links, skip to step 5.
2.	First, setup a custom URI scheme for Android by modifying your AndroidManifest.xml. 
Set the scheme to your application ID and make sure you set the host.  You should end up with something like this:
<intent-filter android:label="@string/filter_view_auth ">
     <action android:name="android.intent.action.VIEW" />
     <category android:name="android.intent.category.DEFAULT" />
     <category android:name="android.intent.category.BROWSABLE" />
     <!-- Accepts URIs that begin with "com.example.app://ndi” ->
     <data android:scheme="com.example.app"
           android:host="ndi" />
</intent-filter>
3.	Setup app links in your AndroidManifest.xml.
Set the scheme to "https" and the host with a domain you own.  Make sure you can serve content on it.  You should end up with something like this:
<intent-filter android:label="@string/filter_view_auth" android:autoVerify="true">
     <action android:name="android.intent.action.VIEW" />
     <category android:name="android.intent.category.DEFAULT" />
     <category android:name="android.intent.category.BROWSABLE" />
     <!-- Accepts URIs that begin with "https://example.com” ->
     <data android:scheme="https"
           android:host="example.com" />
</intent-filter>
4.	You need to declare the host’s association with the app by creating a Digital Asset Links JSON file. 
For example, you may host it at 
https://example.com/.well-known/assetlinks.json.  You would need the application ID for package_name and the SHA256 fingerprints of your signing certificate for sha256_cert_fingerprints.  It should look something like this:
[{
  "relation": ["delegate_permission/common.handle_all_urls"],
  "target": {
    "namespace": "android_app",
    "package_name": "com.example.app",
    "sha256_cert_fingerprints": ["14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"]
  }
}]
5.	If you are not using a third party solution go to the next step.  Otherwise please ensure that you create a custom URI scheme with your application ID and enable app links in your third party solution.
6.	You need a redirect URI for both your custom URI scheme and app links that the NDI App can return the authorization code to.  For example, if your redirect URI is {scheme}://{host}/auth then the NDI App will return the authorization code by launching your app using an Intent with the action ACTION_VIEW and data with {scheme}://{host}/auth?state={state}&code={code}.
7.	You can then initiate authentication by launching the NDI app (like how you load a web URL) using an Intent with action ACTION_VIEW with the following data:
•	Custom URI scheme:
ndi://ndi/auth?client_id={client_id}&scope={scope}
&redirect_uri={redirect_uri}&nonce={nonce}&state={state}&code_challenge={challenge}&code_challenge_method={challenge_method}
•	App Links:
https://sandbox.ndi.io/auth?client_id={client_id}&scope={scope}
&redirect_uri={redirect_uri}&nonce={nonce}&state={state}&code_challenge={challenge}&code_challenge_method={challenge_method}
You should launch using an app link if the user has an Android version of 6.0 and above otherwise you should fall back to the custom URI scheme.
Parameters	Description
client_id (required)	The client ID for your application
redirect_uri	The URI that the NDI app would return the authorization code depending if you are using a custom URI scheme or app link
nonce (required)	A randomly generated unique string which will be included in the ID token as a means to prevent replay attacks
state	A randomly generated unique string which will be returned along with the authorization code as a means to prevent CSRF and can also be used to maintain state
code_challenge_method (required)	The method used for transforming the code verifier. The only supported values are "plain" or "S256"
code_challenge (required)	The code challenge derived from the code verifier using the transformation specified by the code challenge method to enforce PKCE.
8.	The NDI App will then make the relevant requests to the ASP to initiate the user authentication. On completion of user authentication, the NDI App will call your redirect URI accordingly.  For example, suppose your redirect URI is https://example.com/redirect:
•	User authentication succeeded, your Mobile App will be called with the authorization code as follows: https://example.com/redirect?state={state}&code={code}
•	User authentication failed, your Mobile App will be called with an error as follows: https://example.com/redirect?error=access_denied&error_reason=user_denied&error_description=Permissions+error
9.	With the received authorization code, your Mobile App can now call the ASP token endpoint to obtain the ID token and access token accordingly.
ASP API Reference
Token Endpoint
The token endpoint consists of the following API:
•	Token Exchange API – this is an API where your Mobile App can use the authorization code obtained from the authorization endpoint to exchange for the security tokens, namely the ID token and access token.  The ID token is a JSON Web Token (JWT) containing information about the identity of the user authenticated.  The access token is a security token which your Mobile App use to access API/resources of a protected domain (e.g. Government or commercial entity such as the Public Housing Agency or a bank). The access token is issued by the Authorization server of the protected domain and may be in the form of a JWT or a randomly generated reference.  Each access token comes with an expiry date, once expired, your Mobile App will have to re-authenticate the user to obtain a new access token, or alternatively, if the refresh token is provided, your Mobile App may use the refresh token to obtain a new access token without the need to re-authenticate the user.
•	Refresh Token API – this is an API which supports the Refresh Token flow.  If your Mobile App is granted the refresh token privilege during Client App Registration, your Mobile App will be issued with a refresh token by the Token Exchange API, in addition to the ID token and access token.  Your Mobile App can use the refresh token subsequently to obtain a new access token for the user without the need for the user to perform user authentication again.  Refresh tokens have expiry too, but usually much long-lived than access tokens hence it is important to safe keep fresh tokens in a secure manner.  
Token Exchange API
The Token API is accessed via HTTPS/REST as follows:
Request
POST {basePath}/asp/token
Required
Parameter	In	Type	Description
code	Body	String	The authorization code obtained from the authorization endpoint
client_id	Body	String	The client id assigned to your Mobile App during Client App Registration
client_secret	Body	String	The client secret that you obtained for your Mobile App during Client App Registration.  This parameter may be used to hold a digital signature for certificate-based client authentication
redirect_uri	Body	String	The redirect URI of your Mobile App
grant_type	Body	String	Set to "authorization_code", as defined in OAuth 2.0 specifications
code_verifier	Body	String	The PKCE code verifier string generated by your Mobile App which will be used by the ASP to derive the code challenge, to compare with the code challenge value sent to the authorization endpoint. 


Response
Success, the ID token and access token are returned
HTTP/1.1 200 OK
Content-Type: application/json
{
    "access_token" : "95c06f5a-26d4-11e8-b467-0ed5f89f718b"
    "id_token" : "eyJ0eXAiOiJKV1QiLCJhbGciOiJFUzI1NiIsImtpZCI6ImdhbnltZWRlIn0.eyJjbGllbnRfaWQiOiJjb2 1lb25zcHVycyIsImV4cCI6MTIzMjEzNDEyNCwiaWF0IjoyNDEyNDEyMTM3NX0.W_4ViMyL4VhbQRvfLYb2uXSaTmrDWmtd8SGH971ORR6shpWZRoMprzGPcR8VVkwKVYvl0H_8NWSaMudnsGxwFg"
    "expires_in" : 600
    "token_type" : "Bearer"
    “refresh_token” : “24cd99ba-35da-28e4-b76e-4ef5e80f700f”
}
Parameter	In	Type	Description
access_token	Body	String	The access token which can be used to access API and resources of your target protected domain.  The access token may be in the form of a JWT or reference string, depending on the Authorization Server of the protected domain.
id_token	Body	String	The ID token issued by ASP, in the form of a JWT containing identity info about the user authenticated with NDI-approved form factor.  Your Mobile App will have to decode the JWT to inspect the claims in the ID token.
expires_in	Body	String	The lifetime (in seconds) of the access token.  For example, the value "600" denotes the access token will expire in 10 mins from the time the response was generated. 
token_type	Body	String	The type of access token returned, which is always "Bearer".
refresh_token	Body	String	The refresh token (if any, depending on the profile defined for the client app during Client App Registration) which the client app can use to refresh the access token.

Client Error, invalid request (e.g. missing parameters)
HTTP/1.1 400 Bad Request
Content-Type: application/json
{
  "err_msg" : {error_message}
}

Client Error, invalid endpoint URL
HTTP/1.1 404 Not Found


Server Error, server-side errors encountered
HTTP/1.1 500 Internal Server Error
Content-Type: application/json
{
  "err_msg" : {error_message}
}

Refresh Token API
The Refresh Token API let a client app obtain a new access token without going through user authentication.  The client app must supply the refresh token it obtained from the Token Exchange API to the Refresh Token request as follows:
Request
POST {basePath}/asp/token
Required
Parameter	In	Type	Description
client_id	Body	String	The client id assigned to your client app during Client App Registration
client_secret	Body	String	The client secret that you obtained for your client app during Client App Registration
grant_type	Body	String	Set to “refresh_token”, as defined in OAuth 2.0 specifications
refresh_token	Body	String	The refresh token obtained from the token endpoint (Token Exchange API)
Optional
Parameter	In	Type	Description
scope	Body	String	Space delimited and case-sensitive list of strings of OAuth 2.0 scope values, indicating the scope of access requested. Each scope value references a scope profile defined for your client app during Client App Registration.  If not provided, default to "openid"

Response
Success, the ID token and access token are returned
HTTP/1.1 200 OK
Content-Type: application/json
{
    "access_token" : "95c06f5a-26d4-11e8-b467-0ed5f89f718b"
    "token_type" : "Bearer"
    "expires_in" : 600
    “refresh_token” : “77a906e51-94d4-26e8-cd6f-aed2fb9fa38e”
}
Parameter	In	Type	Description
access_token	Body	String	The access token which can be used to access API and resources of your target protected domain.  The access token may be in the form of a JWT or reference string, depending on the Authorization Server of the protected domain.
expires_in	Body	String	The lifetime (in seconds) of the access token.  For example, the value "600" denotes the access token will expire in 10 mins from the time the response was generated. 
token_type	Body	String	The type of access token returned, which is always "Bearer".
refresh_token	Body	String	The new refresh token (if any) which the client app can use to refresh the access token.

Client Error, invalid request (e.g. missing parameters)
HTTP/1.1 400 Bad Request
Content-Type: application/json
{
  "err_msg" : {error_message}
}

Client Error, invalid endpoint URL
HTTP/1.1 404 Not Found


Server Error, server-side errors encountered
HTTP/1.1 500 Internal Server Error
Content-Type: application/json
{
  "err_msg" : {error_message}
}


Discovery Document
The OpenID Connect specifications involve the use of multiple endpoints for user authentication, and the requests for resources such as the security tokens and the public keys to verify these tokens.  To simplify the implementation and discovery of these endpoints and resources, OpenID Connect provides for the use of a Discovery document – in JSON format, and downloadable from a well-known location – which describes the OpenID Connect Provider’s configuration and supported features. 
You will be able to download the ASP Discovery document from the following location: 
GET {basePath}/.well-known/openid-configuration
Your Mobile App should download the ASP Discovery document periodically (it is safe to download the document on a daily basis) to keep up to date of the ASP’s configuration, as the ASP endpoints may change from time to time (e.g. from api/v1 to api/v2), and the signing keys are also refreshed regularly.
The following shows a sample of the ASP discovery document:
{
  “issuer” : “https://centaur1.asp.ndi.io”,
  “authorization_endpoint” : “https://wog.ndi.io/v1/asp/auth”,
  “token_endpoint” : “https://wog.ndi.io/v1/asp/token”,
  “jwks_uri” : “https://wog.ndi.io/v1/certs”,
  “response_types_supported” : [
    “code”
  ],
  “subject_types_supported” : [
    “public”
  ],
  “id_token_signing_algo_values_supported” : [
    “ES256”,
    “RS256”
  ],
  “scope_supported” : [
    “openid”
  ],
  “token_endpoint_auth_methods_supported” : [
    “client_secret_post”
  ],
  “claim_supported” : [
    “aud”,
    “exp”,
    “iat”,
 “iss”,
    “sub”
  ],
  “code_challenge_methods_supported” : [
    “plain”
    “S256”
  ]
}

Security Tokens
ID Token
The ID token is issued in the form of a JWT, the following table shows the format of the ID token and the checks to perform by your Mobile App to ensure it is a valid ID token issued by the ASP.
Parameter	Description	Checks to Perform at the Mobile App
JOSE Header
typ	“JWT”	
alg	Indicates the signature algorithm used to sign this ID token.	The signature algorithm must be one of the supported asymmetric cryptography – e.g. “ES256”, “RS256”.
Must not be “NONE”.
kid	The key id indicating which ASP key was used to sign this ID token.  The ASP uses a set of signing keys whose corresponding public keys are made available in the form of the JWK (JSON Web Key) set.  The kid parameter is used to identify the right public key in the JWK set to verify the signature.	The Mobile App must use the public key indicated by the kid parameter to verify the signature of this ID token to ensure it is a valid ID token issued by the ASP.
The Mobile App is to regularly download the ASP JWK set from the ASP endpoint indicated in the discovery document, as the ASP will periodically refresh its signing keys.  It would be considered safe to download the JWK set on a daily basis.
JWT Payload
iss	The ASP id of the ASP that issued this ID token	Ensure the ASP id points to an ASP that is recognised by your Mobile App.
aud	The client id of your Mobile App	Ensure this matches the client id of your Mobile App.
sub	The uuid of the user who was authenticated	Ensure this matches the value in your user record of the user.
iat	The time this ID token was issued, in Unix time (seconds)	Ensure the ID token issue timestamp is within the tolerance of your Mobile App, i.e. it should not be issued too long ago.
exp	The time this ID token expires, in Unix time (seconds)	Ensure the ID token has not expired.
nonce	The value of the nonce provided by your Mobile App in the request to the ASP authorization endpoint	Your Mobile App is to check that this value matches the copy in its cache, to protect against replay attacks.
JWT Signature
signature	This is the digital signature over the JOSE Header and JWS Payload, using the algorithm specified in the alg Header parameter	Signature created with the ASP’s signing key.  Must be valid.  Verify the signature using the public key indicated by the JOSE Header kid parameter.  See Checks to Perform remarks of the Header kid parameter for more details.
Access Token
The ASP does not generate access tokens, it obtains them from the Authorization Service of the protected domain.  Format of the access token varies from protected domain to protected domain.  There are 2 main approaches of implementing access tokens: 
Self-contained access token – A self-contained access token encapsulates all the authorization assertions and other assertions (e.g. issuer id, token validity period, scope) into the token payload, with the payload signed by the Authorization Service to prevent it from tampering. The payload can also be encrypted to ensure confidentiality.  The JSON Web Token (JWT) structure is widely used to implement the self-contained access token.  The advantage of the self-contained access token is that the API Gateway or the target API can verify the integrity and the authorization assertions by simply examining the access token, without the need to check back with the Authorization Server.  
Reference-based access token -  A reference-based access token is essentially a randomly generated unique reference to an internal table containing the authorization and other assertions maintained by the Authorization Server.  The advantage of reference-based access token is that there is no need for secret keys or key-pairs to sign/encrypt access tokens, however the API Gateway or the target API will have to check back with the Authorization Server to verify the access tokens.
Refresh Token
The ASP does not generate refresh tokens, it receives them from the Authorization Service of the protected domain together with the access tokens.  Format of the refresh token varies from protected domain to protected domain, usually in the form of a randomly generated string which is a reference to some internal record of systems maintained by the Authorization Server. 
Safe Keeping of Security Tokens
The ID token, access token and refresh token are highly confidential data, which if stolen by malicious parties will allow them to gain unauthorized access to API and resources in protected domains.  It is the responsibility of your Mobile App to store these tokens securely and safely dispose them after use.  As a security requirement, you will have to provide a detail description of how to securely store the tokens in your Mobile App.  Some protected domains may only allow their access tokens to be stored on a server in a controlled environment and only allow calls to their API from a whitelisted location.
Exception Handling
General Error Handling
Your Mobile App is to handle error responses from the NDI App and the ASP properly to provide a smooth and pleasant user experience.  For example, error messages returned by the ASP usually contains technical details which may confuse and alarm the user unnecessarily.  Your Mobile App should not simply display these error messages verbatim as and when errors occur, instead your Mobile App should interpret these errors and take appropriate remedial action where possible, and display user-friendly messages when user attention is required.
The following are a non-exhaustive list of errors and the proper way of exception handling to implement.
Authentication Error
Your Mobile App should check for error when the NDI App calls your callback method.  If there is an error, your Mobile App should allow the user to re-attempt login for up to 3 – 5 times.
Client Error (HTTP 4xx)
Client errors are mostly caused by problems at the client side (i.e. your Mobile App), such as badly formed requests, missing mandatory fields, invalid URL, etc.  There is no point retrying when your Mobile App encountered Client Errors, ensure your Mobile App handles user entry properly to eliminate bad request and missing fields issues.
Server Error (HTTP 500)
Server errors are mostly caused by problems at the server side (i.e. the ASP), which might be temporal in nature.  Your Mobile App should retry the request a couple of times and should display a “Service Unavailable” message if the same error persisted.
Timeout Error
Timeout errors may be caused by network congestion or system encountering high load, which may be temporal in nature.  Your Mobile App should retry the request a couple of times but use the exponential backoff approach to avoid further contributing to the congestion.   Display the “Service Unavailable” message if the timeout error persisted.
No Response
Your Mobile App may receive no response when there is a network or system outage.  Your Mobile App should implement timeout to prevent your users from waiting indefinitely.  Display the “Service Unavailable” message if the no response situation persisted 
Error Codes
