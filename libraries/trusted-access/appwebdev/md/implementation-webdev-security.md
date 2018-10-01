### Security Tokens
<br/>

#### ID Token
<br/>

The ID token is issued in the form of a JWT, the following table shows the format of the ID token and the checks to perform by your Web App to ensure it is a valid ID token issued by the ASP.

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
<td><p>The signature algorithm must be one of the supported asymmetric cryptography – e.g. “ES256”, “RS256”.</p>
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
<td>The uuid of the user who was authenticated</td>
<td>Ensure this matches the value in your user record of the user.</td>
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

#### Access Token
<br/>

The ASP does not generate access tokens, it obtains them from the Authorization Service of the protected domain.  Format of the access token varies from protected domain to protected domain.  There are 2 main approaches of implementing access tokens: 

- Self-contained access token – A self-contained access token encapsulates all the authorization assertions and other assertions (e.g. issuer id, token validity period) into the token payload, with the payload signed by the Authorization Service to prevent it from tampering. The payload can also be encrypted to ensure confidentiality.  The JSON Web Token (JWT) structure is widely used to implement the self-contained access token.  The advantage of the self-contained access token is that the API Gateway can verify the integrity and the authorization assertions by simply examining the access token, without the need to check back with the Authorization Service.  

- Reference-based access token -  A reference-based access token is essentially a unique randomly generated reference to an internal table containing the authorization and other assertions maintained by the Authorization Service.  The advantage of reference-based access token is that there is no need for secret keys or key-pairs to sign/encrypt access tokens, however the API Gateway will have to check back with the Authorization Service to verify the access tokens.

#### Refresh Token
<br/>

The ASP does not generate refresh tokens, it receives them from the Authorization Service of the protected domain together with the access tokens.  Format of the refresh token varies from protected domain to protected domain, usually in the form of a randomly generated string which is a reference to some internal record of systems maintained by the Authorization Server.

#### Safe Keeping of Security Tokens
<br/>

The ID token, access token and refresh token are highly confidential data, which if stolen by malicious parties will allow them to gain unauthorized access to API and resources in protected domains.  It is the responsibility of your Web App to store these tokens securely and safely dispose them after use.  These tokens must be stored in a controlled server environment and must not be sent to your frontend code residing in the user’s desktop or mobile device.