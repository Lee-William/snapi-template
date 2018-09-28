### Mobile Digital Signing Flow
<br/>

![Mobile Digital Signing Flow](..\img\mobiledsflow.png)

The Digital Signature Application Provider (DSAP) may choose to use NDI to authenticate the user, so that the correct document is presented to the user. However, this is not discussed in the scope of the present document. You may refer to “Technical Guide – Client App (Web)” for the details.

1. It is recommended to authenticate the user using NDI, for high risk transaction. An OIDC id token and access token will be issued, which you must use to access HSS APIs. (See “Technical Guide – Client App (Web)” for the details.)

2. If you are not authenticating the user using NDI, you have to obtain a client credential grant from the ASP Authorisation Endpoint using your client info. 

You should collect NDI Id from the user through the client app,so that together with security tokens obtain in “Gain access”, are provided when composing the Sign Request. 

The HSS signing flow mechanism utilizes a distributed approach as follows:
+ On receiving the sign request, the HSS obtains the end-user’s form factor attributes from the NDI User Directory, based on the NDI id.  The form factor attributes consist of the end-user’s preferred form factor type, the form factor address (e.g. push notification id), etc.  In this case, the end-user’s preferred form factor is the NDI Soft Token. The HSS then routes the sign notification via push notification to the NDI App residing on the end-user’s mobile device where the soft token form factor resides.

+ The end-user receives the push notification on his mobile device, inspects the description (e.g. name of the client app and scope of access requested) and accepts the sign request.  In accepting the sign request, the end-user may have to enter a PIN (or use phone-specific biometric) to unlock the soft token.

+ The soft token verifies the sign request, generates a signature value with the end-user’s private key and returns the sign response to the HSS via the NDI App. 

+ The HSS verifies the signature of the signed response with the end-user’s digital certificate, after checking the integrity of digital certificate by verifying the certificate chain of trust, and the status of the digital certificate with the CA’s Online Certificate Status Protocol (OCSP) service.

+ On successful verification, the Web App receives the sign response from the HSS via redirect URI provided in the sign request or client registry.

+ The Web App uses the signature, user’s certificate, and OCSP responses within the sign response, to construct the required signature structure and embed it into the document.

### Hash Signing Service APIs
<br/>

#### SignHash API
<br/>

Your Web App uses this endpoint to initiate the user signing flow.  The Web App is to redirect the user agent to this endpoint, which typically returns the NDI Login page for the user to enter his NDI Id for identification. The HSS then routes the sign request to the user's form factor (e.g. the NDI soft token on his mobile device) via the appropriate protocols. In the case of NDI soft token, the sign request is sent to the user via push notification to his mobile device, where the user enters his PIN to unlock the soft token to sign on the document’s hash. The sign response is returned to the HSS which verifies the signature with the user's certificate.  If all is well, the HSS returns the sign response to the Web App via HTTP redirect. The destination of the HTTP redirect is the redirect_uri parameter that you have configured for your Web App during Client App Registration.  

> Redirect URI 

As part of the security model, if your Web App passed to the signHash endpoint a redirect_uri that has not been configured during Client App Registration, the HSS will respond with an error. This is to protect against phishing of the HSS endpoints which let a malicious party replaces the redirect_uri with its own address to obtain the sign response.

To ensure the sign response is returned to a server in a controlled environment instead of frontend code residing in the user’s desktop/mobile browser, the redirect_uri must be a legit domain name under your control which can be whitelisted by the HSS.

> Request

````
GET {basePath}/hss/signatures/signHash
````

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
<td>The client secret that you obtained for your Web App during Client App Registration.</td>
</tr>
<tr class="even">
<td>nonce</td>
<td>Body</td>
<td>String</td>
<td>A random unique reference generated by the Web App, which will be included in the sign response returned by the HSS. The Web App is to match the value of the state returned with its copy to ensure the redirect is from the HSS. The Web App may use this to tie the sign response to a particular signing session, e.g. to mitigate replay attacks, CSRF etc. or restore the previous state of the Web App.</td>
</tr>
<tr class="odd">
<td>hash_alg</td>
<td>Body</td>
<td>String</td>
<td>Indicates the secure hash algorithm that is used to create the hash. This is defaulted to &quot;sha2&quot;, which is the minimum requirement supported by NDI. (see “Hash Algorithms” table below for supported algorithm and their represented value).</td>
</tr>
<tr class="even">
<td>hash</td>
<td>Body</td>
<td>String</td>
<td>This is the message digest produced by applying a hash function on a document. This value shall be displayed on Web App to allow user to perform visual validation for the signing session. The NDI App will display this value as part of the sign request.</td>
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
<td>login_hint</td>
<td>Body</td>
<td>String</td>
<td>The Web App may capture the user's NDI Id using its UI instead of the NDI Login page. To suppress the NDI Login page, it may pass the NDI Id to the HSS by setting this field to the NDI Id obtained.</td>
</tr>
<tr class="even">
<td>redirect_uri</td>
<td>Body</td>
<td>String</td>
<td>The redirect URI of your Web App. When provided, URI must match the value provided during client app registration.</td>
</tr>
<tr class="odd">
<td>scope</td>
<td>Body</td>
<td>String</td>
<td>Space delimited and case-sensitive list of strings of functional scope values, indicating the scope of access requested. Each scope value references a scope profile defined for the WebApp during Client App Registration. If not provided, default to &quot;signHash&quot;</td>
</tr>
<tr class="even">
<td>response_type</td>
<td>Body</td>
<td>String</td>
<td>Indicates the sign response to be used. This is defaulted to &quot;json&quot; for the signing flow.</td>
</tr>
<tr class="odd">
<td>tx_id</td>
<td>Body</td>
<td>String</td>
<td>The Web App may provide the transaction id assigned to the current signing session. If specified, the HSS will include it in the sign response to the Web App’s redirect URI. Alternatively, the Web App can rely on “state” parameter.</td>
</tr>
<tr class="even">
<td>tx_expiry</td>
<td>Body</td>
<td>String</td>
<td>The Web App may provide the date-time given to process the request before timeout in yyyy-mm-dd<strong>T</strong>hh:mm:ss+08:00 e.g. 2018-06-21T18:36:36+08:00. HSS can rely on this value to provide the sign response before session timeout on the Web App. If this parameter is not specified, a default timeout of 600 seconds is used.</td>
</tr>
<tr class="odd">
<td>tx_doc_name</td>
<td>Body</td>
<td>String</td>
<td>The document name displayed on Web App to allow user to perform visual validation for the signing session. If specified, the NDI App will display this value as part of the sign request.</td>
</tr>
<tr class="even">
<td>tx_vcode</td>
<td>Body</td>
<td>String</td>
<td>A random 6-digit numeric generated and displayed on Web App to allow user to perform visual validation for the signing session. If specified, the NDI App will display this value as part of the sign request.</td>
</tr>
</tbody>
</table>

> Response
```
Success, the NDI Login page is returned

HTTP/1.1 200 OK
Content-Type: text/html
Content: HTML of the NDI Login page
````

````
Success, the sign response is returned</strong></th>
HTTP/1.1 200 OK</p>
Content-Type: application/json</p>
{
    “uuid”: "63e514c0-5e67-11e8-9e55-e138197c233b",
    “usr_action”: “accept”,
    “jws”: “&lt;header&gt;.&lt;payload&gt;.&lt;signature&gt;”
}
````

<table>
<tr class="even">
<td><strong>Parameter</strong></td>
<td><strong>In</strong></td>
<td><strong>Type</strong></td>
<td><strong>Description</strong></td>
</tr>
<tr class="odd">
<td>uuid</td>
<td>Body</td>
<td>String</td>
<td>The uuid of the user who signed this transaction</td>
</tr>
<tr class="even">
<td>usr_action</td>
<td>Body</td>
<td>String</td>
<td>The user’s action to accept or reject this transaction</td>
</tr>
<tr class="odd">
<td>jws</td>
<td>Body</td>
<td>String</td>
<td>The signed token issued by HSS, in the form of a JWT containing identity info about the user and signed data with NDI-approved form factor. Your Web App will have to decode the JWT to inspect the claims and data in the signed token.</td>
</tr>
</tbody>
</table>

<table>
<thead>
<tr class="header">
<th><strong>Interpreting JWS</strong></th>
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
<td>iss</td>
<td>Payload</td>
<td>String</td>
<td>The HSS id that issued this token</td>
</tr>
<tr class="odd">
<td>aud</td>
<td>Payload</td>
<td>String</td>
<td>The client id of your web app</td>
</tr>
<tr class="even">
<td>txn: nonce</td>
<td>Payload</td>
<td>String</td>
<td>The value of the nonce provided by your Web App in the request</td>
</tr>
<tr class="odd">
<td>signData: signature</td>
<td>Payload</td>
<td>String</td>
<td>the signature value obtained from user’s device</td>
</tr>
<tr class="even">
<td>sign_time</td>
<td>Payload</td>
<td>String</td>
<td>The time the user accept the sign request, in Unix time (seconds)</td>
</tr>
<tr class="odd">
<td>iat</td>
<td>Payload</td>
<td>String</td>
<td>The time this token is issued, in Unix time (seconds)</td>
</tr>
<tr class="even">
<td>cert_PEM</td>
<td>Payload</td>
<td>String</td>
<td>The PEM of the user’s certificate, which can be used to verify the signature, obtain OCSP, and verify signer’s identity.</td>
</tr>
</tbody>
</table>

<table>
<thead>
<tr class="header">
<th><strong>Redirected, on error, the HSS returns the error code via HTTP redirect to the Web App's redirect URI.</strong></th>
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
<p>&quot;err_msg&quot; = &quot;invalid client id&quot;</p>
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
<td><p>HTTP/1.1 400 Bad Request</p>
<p>Content-Type: application/json</p>
<p>{</p>
<p>“error”:”invalid_request”,</p>
<p>&quot;error_description&quot;:”This is not a valid sign request”</p>
<p>}</p></td>
</tr>
</tbody>
</table>

#### Trusted List (aka Discovery Document)

> To verify the sign response, there will be requests for resources such
> as the security tokens and the public keys. To simplify the
> implementation and discovery of these endpoints and resources, you can
> use a discovery document – in JSON format, and downloadable from a
> well-known location – which describes the configuration and supported
> features.
>
> You will be able to download the HSS discovery document from the
> following location:
>
> **GET {basePath}/hss/discovery**
>
> Your Web App should download the HSS discovery document periodically
> (it is safe to download the document on a daily basis) to keep up to
> date of the HSS’s configuration, as the HSS endpoints may change from
> time to time (e.g. from api/v1 to api/v2), and the public keys are
> also refreshed regularly.

#### Security Tokens
<br/>

##### ID Token

> If your signature application is authenticating the user against NDI
> ASP, an ID token will be issued upon acknowledge by the user.
>
> The ID token is issued in the form of a JWT, the following table shows
> the format of the ID token and the checks to perform by your Web App
> and HSS to ensure it is a valid ID token issued by the ASP. A valid ID
> token will allow HSS to obtain a valid NDI user attributes and
> registered client attributes.
>
> The JSON Web Token (JWT) structure is widely used to implement the
> self-contained id token. The advantage of the self-contained id token
> is that the API Gateway can verify the integrity and the identity
> assertions by simply examining the id token, without the need to check
> back with the identity provider. However, additional effort is
> required to validate the revocation status of the identity, which is
> beyond the scope of this document.

<table>
<thead>
<tr class="header">
<th>Parameter</th>
<th>Description</th>
<th>Checks to Perform at the Web App</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>JOSE Header</td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td>typ</td>
<td>“JWT”</td>
<td></td>
</tr>
<tr class="odd">
<td>alg</td>
<td>Indicates the signature algorithm used to sign this ID token.</td>
<td><p>The signature algorithm must be one of the asymmetric cryptography – e.g. “ES256”.</p>
<p>Must not be “NONE”.</p></td>
</tr>
<tr class="even">
<td>kid</td>
<td>The key id indicating which ASP key was used to sign this ID token. The ASP uses a set of signing keys whose corresponding public keys are made available in the form of the JWK (JSON Web Key) set. The kid parameter is used to identify the right public key in the JWK set to verify the signature.</td>
<td><p>The Web App must use the public key indicated by the kid parameter to verify the signature of this ID token to ensure it is a valid ID token issued by the ASP.</p>
<p>The Web App is to download the ASP JWK set from the ASP discovery endpoint on a regular basis, as the ASP will periodically refresh its signing keys. It would be considered safe to download the JWK set on a daily basis.</p></td>
</tr>
<tr class="odd">
<td>JWT Payload</td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td>iss</td>
<td>The ASP id of the ASP that issued this ID token</td>
<td>Ensure the ASP id points to an ASP that is recognised by your Web App.</td>
</tr>
<tr class="odd">
<td>aud</td>
<td>The client id of your Web App</td>
<td>Ensure this matches the client id of your Web App.</td>
</tr>
<tr class="even">
<td>sub</td>
<td>The uuid of the subject/ndi user</td>
<td>{to reference to ASP doc}</td>
</tr>
<tr class="odd">
<td>iat</td>
<td>The time this ID token was issued, in Unix time (seconds)</td>
<td>Ensure the ID token issue timestamp is within the tolerance of your Web App, i.e. it should not be issued too long ago.</td>
</tr>
<tr class="even">
<td>exp</td>
<td>The time this ID token expires, in Unix time (seconds)</td>
<td>Ensure the ID token has not expired.</td>
</tr>
<tr class="odd">
<td>nonce</td>
<td>The value of the nonce provided by your Web App in the request to the ASP authorization endpoint</td>
<td>Your Web App is to check that this value matches the copy in its cache, to protect against replay attacks.</td>
</tr>
<tr class="even">
<td>JWT Signature</td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>signature</td>
<td>This is the digital signature over the JOSE Header and JWS Payload, using the algorithm specified in the <em>alg</em> Header parameter</td>
<td>Signature created with the ASP’s signing key. Must be valid. Verify the signature using the public key indicated by the JOSE Header kid parameter. See Checks to Perform remarks of the Header kid parameter for more details.</td>
</tr>
</tbody>
</table>

##### Access Token

> All NDI APIs are protected by access tokens. Relying party must obtain
> them from the NDI Authorization Service. The HSS does not generate
> access tokens, it verify the access token i.e. validate the issuer,
> scope within the token. Format of the access token will be a
> Self-contained access token with “signHash” scope:
>
> *Self-contained access token* – A self-contained access token
> encapsulates all the authorization assertions and other assertions
> (e.g. issuer id, token validity period) into the token payload, with
> the payload signed by the Authorization Service to prevent it from
> tampering. The payload can also be encrypted to ensure
> confidentiality. The JSON Web Token (JWT) structure is widely used to
> implement the self-contained access token. The advantage of the
> self-contained access token is that the API Gateway can verify the
> integrity and the authorization assertions by simply examining the
> access token, without the need to check back with the Authorization
> Service.

#### Exception Handling

##### General Error Handling

> Your Web App is to handle error responses from the HSS properly to
> provide a smooth and pleasant user experience. For example, error
> messages returned by the HSS usually contains technical details which
> may confuse and alarm the user unnecessarily. Your Web App should not
> simply display these error messages verbatim as and when these errors
> occur, instead your Web App should interpret these errors and take
> appropriate remedial action where possible, and display user-friendly
> messages when user attention is required.
>
> The following are a non-exhaustive list of errors and the proper way
> of exception handling to implement.

<table>
<thead>
<tr class="header">
<th><strong>Error</strong></th>
<th><strong>Error Description</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>invalid_request</td>
<td>The request is missing a required parameter, includes an invalid parameter value, includes a parameter more than once, or is otherwise malformed.</td>
</tr>
<tr class="even">
<td>unauthorized_client</td>
<td>The client is not authorized to use this method.</td>
</tr>
<tr class="odd">
<td>access_denied</td>
<td>The user, authorization server or remote service denied the request.</td>
</tr>
<tr class="even">
<td>unsupported_response_type</td>
<td>The authorization server does not support obtaining an authorization code using this method.</td>
</tr>
<tr class="odd">
<td>invalid_scope</td>
<td>The requested scope is invalid, unknown, or malformed.</td>
</tr>
<tr class="even">
<td>server_error</td>
<td>The authorization server encountered an unexpected condition that prevented it from fulfilling the request.</td>
</tr>
<tr class="odd">
<td>temporary_unavailable</td>
<td>The authorization server is currently unable to handle the request due to a temporary overloading or maintenance of the server.</td>
</tr>
<tr class="even">
<td>expired_token</td>
<td>The access token is expired or has been revoked.</td>
</tr>
<tr class="odd">
<td>invalid_token</td>
<td>The token provided is not a valid OAuth access token</td>
</tr>
</tbody>
</table>

##### Anti-spam and anti-bot protection

> If you are using your own login UI to capture the NDI Id of the user,
> ensure your login UI allows the user to re-attempt the login up to 3 –
> 5 trials. Your login UI is to be protected by anti-bot mechanism such
> as captcha.

##### Client Error (HTTP 4xx)

> Client errors are mostly caused by problems at the client side (i.e.
> your Web App), such as badly formed requests, missing mandatory
> fields, invalid URL, etc. There is no point retrying when your Web App
> encountered Client Errors, ensure your Web App handles user entry
> properly to eliminate bad request and missing fields issues.

##### Server Error (HTTP 500)

> Server errors are mostly caused by problems at the server side (i.e.
> the HSS), which might be temporal in nature. Your Web App should retry
> the request a couple of times and should display a “Service
> Unavailable” message if the same error persisted.

##### Timeout Error

> Timeout errors may be caused by network congestion or system
> encountering high load, which may be intermittent in nature. Your Web
> App should retry the request a couple of times but use the exponential
> backoff approach to avoid further contributing to the congestion.
> Display the “Service Unavailable” message if the timeout error
> persisted.

##### No Response

> Your Web App may receive no response when there is a network or system
> outage. Your Web App should implement timeout to prevent your users
> from waiting indefinitely. Display the “Service Unavailable” message
> if the no response situation persisted

##### Error Codes

<table>
<thead>
<tr class="header">
<th><strong>HTTP Status Code</strong></th>
<th><strong>Description</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>200 OK</td>
<td>Response to a successful API method request.</td>
</tr>
<tr class="even">
<td>400 Bad Request</td>
<td>Returned when an error is returned due to an unsupported or invalid parameters or missing required parameters.</td>
</tr>
<tr class="odd">
<td>401 Unauthorized</td>
<td>Return when a bad or expired authorization token is used.</td>
</tr>
<tr class="even">
<td>429 Too Many Requests</td>
<td>Returned when a request is rejected due to rate limiting.</td>
</tr>
<tr class="odd">
<td>500 Internal Server Error</td>
<td>Returned when the server encounters an unexpected conditions</td>
</tr>
<tr class="even">
<td>501 Not Implemented</td>
<td>Returned when an unimplemented method is requested.</td>
</tr>
<tr class="odd">
<td>503 Service Unavailable</td>
<td>Returned when the server is currently unable to handle the request due to temporary overloading or maintenance conditions.</td>
</tr>
</tbody>
</table>

##### Hash Algorithms

<table>
<thead>
<tr class="header">
<th><strong>Full Name</strong></th>
<th><strong>Supported in NDI</strong></th>
<th><strong>NDI Parameter Value</strong></th>
<th><strong>Expected hash value (size in bit)</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>MD5</td>
<td>No</td>
<td>Not applicable</td>
<td>128</td>
</tr>
<tr class="even">
<td>SHA-1</td>
<td>No</td>
<td>Not applicable</td>
<td>160</td>
</tr>
<tr class="odd">
<td>SHA-224</td>
<td>No</td>
<td>Not applicable</td>
<td>224</td>
</tr>
<tr class="even">
<td>SHA-256</td>
<td>Yes</td>
<td>sha2</td>
<td>256</td>
</tr>
<tr class="odd">
<td>SHA-384</td>
<td>Yes</td>
<td>sha2</td>
<td>384</td>
</tr>
<tr class="even">
<td>SHA-512</td>
<td>Yes</td>
<td>sha2</td>
<td>512</td>
</tr>
<tr class="odd">
<td>SHA-512/256</td>
<td>Yes</td>
<td>sha2</td>
<td>256</td>
</tr>
<tr class="even">
<td>SHA3-224</td>
<td>Yes</td>
<td>sha3</td>
<td>224</td>
</tr>
<tr class="odd">
<td>SHA3-256</td>
<td>Yes</td>
<td>sha3</td>
<td>256</td>
</tr>
<tr class="even">
<td>SHA3-384</td>
<td>Yes</td>
<td>sha3</td>
<td>384</td>
</tr>
<tr class="odd">
<td>SHA3-512</td>
<td>Yes</td>
<td>sha3</td>
<td>512</td>
</tr>
</tbody>
</table>

##### Signature Algorithms

<table>
<thead>
<tr class="header">
<th><strong>Full Name</strong></th>
<th><strong>Supported in NDI</strong></th>
<th><strong>NDI Parameter Value</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>NIST K-###</td>
<td>No</td>
<td>Not applicable</td>
</tr>
<tr class="even">
<td>NIST B-###</td>
<td>No</td>
<td>Not applicable</td>
</tr>
<tr class="odd">
<td><p>Prime256v1</p>
<p>NIST P-256</p></td>
<td>Yes</td>
<td>secp256r1</td>
</tr>
<tr class="even">
<td>NIST P-384</td>
<td>Yes</td>
<td>secp384r1</td>
</tr>
<tr class="odd">
<td>NIST P-521</td>
<td>Yes</td>
<td>secp521r1</td>
</tr>
</tbody>
</table>
