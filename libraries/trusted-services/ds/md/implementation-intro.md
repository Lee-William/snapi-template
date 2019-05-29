### Hash Signing Service (HSS) Overview
<br/>

Coordination and management of the digital signature process represents an opportunity to define a Hash Signing Service for citizens and application providers alike. Such an approach might:

+ Accelerate adoption of digital signature by Digital Signature Application Providers (DSAP) (and consequently adoption by citizens).
  
+ Allow implementation/deployment of a standardised API

+ Permit access to an existing base of endusers possessing SingPass accounts

+ Coordinate activation of digital signature functionality for endusers
  
+ Coordinate the processing of signature requests for DSAP

+ Provide a manageable approach to risk reduction
  
NDI HSS provides a standardised means to form a digital signature using NDI-provisioned digital identity (using form factors such as NDI soft token)
The HSS is essentially a signing service, the API is defined to standardise the communication with the DSAP :

1. Signature request via (OpenID's Client Initiated Backchannel Authentication (CIBA) Push Mode)[https://openid.net/specs/openid-client-initiated-backchannel-authentication-core-1_0.html#rfc.section.5]

2. Signature response via (OpenID's Client Initiated Backchannel Authentication (CIBA) Push Mode)[https://openid.net/specs/openid-client-initiated-backchannel-authentication-core-1_0.html#rfc.section.5]

Typically, this means that a DSAP will trigger the signing request and the HSS will relay the request to the user’s registered mobile device, where the user can use the NDI provisioned digital identity to create a digital signature.


#### Digital Signing Flow for QR Code
<br/>

The Digital Signature Application Provider (DSAP) is required to ensure that the user is logged onto the its respective digital signing platform so that the correct document/s are presented to the user. 


The HSS signing flow for QR utilizes a 2-part approach as follows:


![Digital Signing Flow QR Flow: 1st Leg](/assets/lib/trusted-services/ds/img/first-leg.png)

1. The first leg begins with the RP/DSP initiating a GET call to the QR authentication endpoint. This results in a signature reference object that is meant to be encoded as a QR image and displayed on the front-end of the DSP/RP's application.
      
2. The end-user shall use his/her NDI Mobile App to scan the QR. This will result in the capturing of the QR Content. Implicitly, the user also provides consent to allow the release of his/her public certificate to the RP/DSP.
      
3. The end user's form-factor invokes HSS's internal endpoint that results in a document signing session created on HSS's backend. The user's public certificate is also passed to HSS via this endpoint
    
4. The user certificate shall be sent to the RP/DSP via its registered Client Notification Endpoint. The signature ref ID and several other CIBA parameters will be passed alongside the user certificate via this endpoint as well. Thus, concluding the first leg.

![Digital Signing Flow QR Flow: 2nd Leg](/assets/lib/trusted-services/ds/img/second-leg.png)

1. The second leg begins with the RP/DSP initiating a POST call to HSS's sign-hash endpoint. The RP/DSP shall make use of this endpoint to pass the PadEs document hash, challenge code and signature reference ID to HSS. A valid request shall return a HTTP Status code of 201.
      
2. The HSS displays the 6-digit challenge code and document hash on the user's form factor. The end-user is then requested to consent on signing on the displayed document hash.

3. The resultant signature shall be sent to the RP/DSP via the Client Notification Endpoint. Thus, concluding the second leg.


#### Digital Signing Flow for Push Notification
<br/>

The Digital Signature Application Provider (DSAP) is required to execute a one-time NDI Login by redirecting the user to NDI's Authentication Endpoint to initiate an OIDC Handshake with NDI's Authentication Service Provider (ASP). However, this is not discussed in the scope of the present document. You may refer to “Technical Guide – Client App (Web)” under the "Trusted Access" section for the details. 

Similar to the QR flow, the HSS signing flow for Push Notification utilizes a 2-part approach. DSP/RPs subscribing to the PN flow should only perform the first-leg as a one-off authentication to retrieve the user's public certificate. The ritual of performing the first-leg will only repeat if the user's public certificate has expired or been revoked.

The 2-part approach is as follow:

![Digital Signing Flow Push Notification Flow: 1st Leg](/assets/lib/trusted-services/ds/img/first-leg-pn.png)

1. The RP/DSP performs a one-time OIDC Handshake with NDI's ASP. After the user performs a successful authentication, his/her OIDC id_token will be issued to the RP/DSP.

2. The RP/DSP performs a POST call to the PN trigger endpoint, passing in the user's id_token under the id_token_hint field. The HSS shall return a digital-signing reference ID or "sign_ref" upon successful invocation.

3. A PN is sent to the user's mobile device.

4. The user consents on releasing his/her public certificate to the DSP/RP.

5. His/her public cerificate will be sent to HSS via an internal endpoint.

6. The user certificate shall be sent to the RP/DSP via its registered Client Notification Endpoint. The signature ref ID and several other CIBA parameters will be passed alongside the user certificate via this endpoint as well. Thus, concluding the first leg.

![Digital Signing Flow Push Notification Flow: 2nd Leg](/assets/lib/trusted-services/ds/img/second-leg-pn.png)

1. The second leg begins with the RP/DSP initiating a POST call to HSS's sign-hash endpoint. 
	+ If the first-leg was performed prior to this operation, the RP/DSP shall make use of this endpoint to pass the PadEs document hash, challenge code and signature reference ID to HSS. A valid request shall return a HTTP Status code of 201.
	+ If the first-leg was NOT performed prior to this operation, the RP/DSP shall make use of this endpoint to pass the PadEs document hash, challenge code and CIBA Request parameters to HSS. A valid request shall return a digital-signing reference ID (sign_ref) with a HTTP Status code of 201.
      
2. The HSS displays the 6-digit challenge code and document hash on the user's form factor. The end-user is then requested to consent on signing on the displayed document hash.

3. The resultant signature shall be sent to the RP/DSP via the Client Notification Endpoint. Thus, concluding the second leg.



