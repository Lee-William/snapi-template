### Discovery Document
<br/>

The OpenID Connect specifications involve the use of multiple endpoints for user authentication, and the requests for resources such as the security tokens and the public keys to verify these tokens.  To simplify the implementation and discovery of these endpoints and resources, OpenID Connect provides for the use of a Discovery document – in JSON format, and downloadable from a well-known location – which describes the OpenID Connect Provider’s configuration and supported features. 
You will be able to download the ASP Discovery document from the following location: 

<b>GET {basePath}/.well-known/openid-configuration</b>

Your Mobile App should download the ASP Discovery document periodically (it is safe to download the document on a daily basis) to keep up to date of the ASP’s configuration, as the ASP endpoints may change from time to time (e.g. from api/v1 to api/v2), and the signing keys are also refreshed regularly.
The following shows a sample of the ASP discovery document:
````
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
````