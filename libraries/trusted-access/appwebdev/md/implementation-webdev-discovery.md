### Discovery Document
<br/>

The OpenID Connect specifications involve the use of multiple endpoints for user authentication, and the requests for resources such as the security tokens and the public keys to verify these tokens. To simplify the implementation and discovery of these endpoints and resources, OpenID Connect provides for the use of a Discovery document

> – in JSON format, and downloadable from a well-known location – which
> describes the OpenID Connect Provider’s configuration and supported
> features.
>
> You will be able to download the ASP Discovery document from the following location:
>
> GET {basePath}/.well-known/openid-configuration**

Your Web App should download the ASP Discovery document periodically (it is safe to download the document on a daily basis) to keep up to date of the ASP’s configuration, as the ASP endpoints may change from time to time (e.g. from /v1 to /v2), and the signing keys are also refreshed regularly.

The following shows a sample of the ASP discovery document:

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