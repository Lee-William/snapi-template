### Exception Handling

#### General Error Handling
<br/>

Your Web App is to handle error responses from the HSS properly to provide a smooth and pleasant user experience. For example, error messages returned by the HSS usually contains technical details which may confuse and alarm the user unnecessarily. Your Web App should not simply display these error messages verbatim as and when these errors occur, instead your Web App should interpret these errors and take appropriate remedial action where possible, and display user-friendly messages when user attention is required.

The following are a non-exhaustive list of errors and the proper way of exception handling to implement.

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

#### Anti-spam and anti-bot protection

> If you are using your own login UI to capture the NDI Id of the user,
> ensure your login UI allows the user to re-attempt the login up to 3 –
> 5 trials. Your login UI is to be protected by anti-bot mechanism such
> as captcha.

#### Client Error (HTTP 4xx)

> Client errors are mostly caused by problems at the client side (i.e.
> your Web App), such as badly formed requests, missing mandatory
> fields, invalid URL, etc. There is no point retrying when your Web App
> encountered Client Errors, ensure your Web App handles user entry
> properly to eliminate bad request and missing fields issues.

#### Server Error (HTTP 500)

> Server errors are mostly caused by problems at the server side (i.e.
> the HSS), which might be temporal in nature. Your Web App should retry
> the request a couple of times and should display a “Service
> Unavailable” message if the same error persisted.

#### Timeout Error

> Timeout errors may be caused by network congestion or system
> encountering high load, which may be intermittent in nature. Your Web
> App should retry the request a couple of times but use the exponential
> backoff approach to avoid further contributing to the congestion.
> Display the “Service Unavailable” message if the timeout error
> persisted.

#### No Response

> Your Web App may receive no response when there is a network or system
> outage. Your Web App should implement timeout to prevent your users
> from waiting indefinitely. Display the “Service Unavailable” message
> if the no response situation persisted

#### Error Codes

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

#### Hash Algorithms

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

#### Signature Algorithms

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
