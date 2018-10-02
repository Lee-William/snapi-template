#### Security Tokens

##### ID Token
<br/>

If your signature application is authenticating the user against NDI ASP, an ID token will be issued upon acknowledge by the user.

The ID token is issued in the form of a JWT, the following table shows the format of the ID token and the checks to perform by your Web App and HSS to ensure it is a valid ID token issued by the ASP. A valid ID token will allow HSS to obtain a valid NDI user attributes and registered client attributes.

The JSON Web Token (JWT) structure is widely used to implement the self-contained id token. The advantage of the self-contained id token is that the API Gateway can verify the integrity and the identity assertions by simply examining the id token, without the need to check back with the identity provider. However, additional effort is required to validate the revocation status of the identity, which is beyond the scope of this document.

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
<td align="left" colspan=3>JOSE Header</td>
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
<td align="left" colspan=3>JWT Payload</td>
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
<td align="left" colspan=3>JWT Signature</td>
</tr>
<tr class="odd">
<td>signature</td>
<td>This is the digital signature over the JOSE Header and JWS Payload, using the algorithm specified in the <em>alg</em> Header parameter</td>
<td>Signature created with the ASP’s signing key. Must be valid. Verify the signature using the public key indicated by the JOSE Header kid parameter. See Checks to Perform remarks of the Header kid parameter for more details.</td>
</tr>
</tbody>
</table>

##### Access Token

All NDI APIs are protected by access tokens. Relying party must obtain them from the NDI Authorization Service. The HSS does not generate
access tokens, it verify the access token i.e. validate the issuer, scope within the token. Format of the access token will be a self-contained access token with “signHash” scope.

*Self-contained access token* – A self-contained access token encapsulates all the authorization assertions and other assertions (e.g. issuer id, token validity period) into the token payload, with the payload signed by the Authorization Service to prevent it from tampering. The payload can also be encrypted to ensure confidentiality. The JSON Web Token (JWT) structure is widely used to implement the self-contained access token. The advantage of the self-contained access token is that the API Gateway can verify the integrity and the authorization assertions by simply examining the access token, without the need to check back with the Authorization Service.