# NDI Fundamentals

## ASP Operating Modes

The ASP may be operated in the 2 modes – as an OIDC Provider, or as a pure-play authenticator.  The operating modes will decide how relying parties (i.e. the client app) and the Authorization Server of the federated site interact with the ASP.  In both modes, the ASP is only responsible for authenticating the user and generates the ID token, it is the organization’s Authorization Server which determines whether the relying party and the user are authorized to access its protected resources and issues the access token accordingly. 

### Mode 1: ASP as an OpenID Connect Provider

![ASP mode 1](\assets\lib\aspop\img\aspmode1.png)

The ASP acts as an OIDC Provider, handling the OIDC flow with the relying party.  This operating mode is useful for organizations which are planning to expose their capabilities through API and may not have a OAuth 2.0/OIDC enabled Authorization Server.  During the OIDC flow, the ASP performs user authentication with the user’s NDI form factor, on successful authentication, it calls the organization’s Authorization Server to obtain an access token.  The Authorization Server determines whether the relying party and user has proper access (based on the organization’s access policy) and generates the access token accordingly.  The ASP returns the access token to the relying party which may then use it to access protected resources.

In this operating mode, the ASP integrates with the Authorization Server of the organization through the Domain Authorization Interface.

### Mode 2: ASP as an Authenticator

![ASP mode 2](\assets\lib\aspop\img\aspmode2.png)
 
The ASP acts as an authenticator service which the Authorization Server calls to perform user authentication with the user’s NDI form factor.  This operation mode is applicable for organizations which are already offering OAuth 2.0 or OIDC based authorization to relying parties accessing their protected resources.  In this scenario, organizations typically use an Authorization Server (or IAM) module to handle the OAuth 2.0/OIDC flows with relying parties.  During the OAuth 2.0/OIDC flow, the Authorization Server module calls the ASP authentication API to perform user authentication with the user’s NDI form factor.  On successful user authentication, the Authorization Server determines whether the relying party and user have the proper access (based on the organization’s access policies), then generates and returns an access token to the relying party which it uses to access protected resources. 

In this operating mode, the Authorization Server of the organization will integrate with the ASP through the ASP Authentication API.

# Federation Approach

These are the 3 approaches which can be taken to setup a federated ASP:
- Cloud ASP Service
- Federated ASP Pack
- Deep Integration

## Cloud ASP Service
This is the simplest option for federation.  The ASP is available as a cloud service, and a dedicated Cloud ASP Service instance may be created for the organization which will operate it.

![cloud ASP approach](\assets\lib\aspop\img\cloudaspapproach.png)

The Cloud ASP Service integrates with the following on-site modules:

|Modules | Description |
| ------ | ----------- |
|Authorization Server of the organization.<br><br>This is the module used by the organization to manage and enforce access control policies on its protected resources.  An IAM product may be used to fulfil this role. | For Mode 1 – ASP as the OIDC Provider the Cloud ASP Service initiates the call to the Authorization Server for token exchange.  The Authorization Server will integrate with the ASP through the Domain Authorization Interface.<br><br> For Mode 2 – ASP as an Authenticator, the Authorization Server initiates the call to the Cloud ASP Service to perform user authentication.  The Authorization Server will integrate with the ASP through the ASP Authentication API. <br><br>Integration requirements:<br>- Internet access of appropriate bandwidth for integration between ASP and the Authorization Server<br>- Certificate-based mTLS secure protocol between ASP and Authorization Server. 
| Relying Parties (i.e. client apps) accessing the protected resources of the organization. | This is only applicable for Mode 1 – ASP as the OIDC Provider.  The organization’s relying parties integrate with the ASP through the ASP OpenID Connect interface. <br><br>Integration requirements:<br>- Internet access of appropriate bandwidth for integration between relying parties and ASP<br>- Certificate-based mTLS secure protocol between relying parties and ASP. |

## Federated ASP Pack

This option is for organizations which have on-premises requirement.  Obtain and deploy pre-built ASP modules at the federated site.  The deployment of the pre-built ASP pack at the federated site is made relatively easy by support for Open Standards and commodity infrastructure.  The deployment steps are largely automated through deployment scripts.

### Deployment Pre-requisites

These are the pre-requisites for Federated ASP Pack deployment to the federated site:
Components	Description
Servers – to run the ASP and DS micro-services	These may be commodity VMs running a Linux based OS (RedHat Enterprise Linux recommended).  The ASP and DS micro-services support load-balancing and horizontal scaling based on virtual IP address. 
A container version of the ASP Core and DS Replica is available to run on PaaS / container platform which supports Docker.
Database – to host the DS data stores	These may be any SQL database (MySQL Enterprise recommended).  
If your existing database is NoSQL, the DS Data Services micro-service will have to be customized to integrate with your NoSQL, as there is currently no industry standards for accessing NoSQL databases.
Hardware Secure Module (HSM) – to store ASP keys	These may be any FIPS L3 certificate HSM which supports PKCS#11.
If your HSM does not support PKCS#11, the ASP Crypto Services micro-service will have to be customized to integrate with your HSM using proprietary interfaces.
Distributed Cache – to store transient data shared by the ASP micro-service instances	These may be any distributed cache which supports the JCache interface (JSR107).
If your distributed cache does not support JCache, the ASP Data Services micro-service will have to be customized to integrate with your distributed cache system.  Alternatively, you may use the distributed cache solution of the ASP Core package. 

### Deployment Overview

The following diagram shows an overview of a Federated ASP Pack deployment with integration points to on-site and off-site components.

![deployment overview](\assets\lib\aspop\img\deploymentoverview.png)

The NDI modules to be deployed in the federated site are:
- ASP Core – comprising a group of micro-services providing the core features of the ASP
- Directory Services (Replica) – comprising the NDI User Directory and the Client Registry data stores

The ASP integrates with the following on-site modules, integration approach to these on-site modules will depend on the operating mode which the organization use to run the ASP.

| Module | Description | 
|--------|-------------|
| Authorization Server of the organization. <br><br>This is the module used by the organization to manage and enforce access control policies on its protected resources.  An IAM product may be used to fulfil this role. | For Mode 1 – ASP as the OIDC Provider, the ASP will initiate the call to the Authorization Server for token exchange.  The Authorization Server will integrate with the ASP through the Domain Authorization Interface.<br><br>For Mode 2 – ASP as an Authenticator, the Authorization Server will be initiating a call to the ASP to perform user authentication.  The Authorization Server will integrate with the ASP through the ASP Authentication API.<br><br>Integration requirements:<br>- Internet access of appropriate bandwidth for integration between ASP and the Authorization Server<br>-Certificate-based mTLS secure protocol between ASP and Authorization Server.
|Relying Parties (i.e. client apps) accessing protected resources of the organization.|This is only applicable for Mode 1 – ASP as the OIDC Provider.  The organization’s relying parties integrate with the ASP through the ASP OpenID Connect interface.<br><br>Integration requirements:<br>- Internet access of appropriate bandwidth for integration between relying parties and ASP<br>- Certificate-based mTLS secure protocol between relying parties and ASP.


The ASP integrates with the following off-site modules:

|Module |	Description|
|-------|--------------|
|Directory Services (Master) – to enable replication of data to the Directory Service Replica on the federated site. | Federated site to allow HTTPS access over Internet for the Directory Service (Master) to call the DS Subscriber, through certificate-based mTLS secure protocol. 
|OCSP (Online Certificate Status Protocol) service – to obtain certificate status during user authentication.|Federated site to allow HTTPS access over Internet for the ASP OIDC Services to call the NDI OCSP service, through certificate-based mTLS secure protocol.
|CA CRL distribution point – to download CRL file as a fallback for certificate status check when the OCSP service is unavailable.|Federated site to download the CRL file periodically from the NDI CA CRL distribution point.  This may be done with HTTP or FTP.  The CRL files to be downloaded to a location accessible by the ASP OIDC Services module.
|Form Factor Authenticator – to initiate user authentication with the user’s preferred form factor.  The ASP comes with a built-in Form Factor Authenticator for the NDI soft token form factor.  However, if the user’s preferred form factor is an alternative form factor (e.g. crypto-SIM), the ASP will need to route the authentication request to the corresponding Form Factor Authenticator which may be hosted remotely.|Federated site to allow HTTPS access over Internet for the ASP OIDC Services to call the remotely hosted Form Factor Authenticator services of available alternative form factors (e.g. crypto-SIM), through certificate-based mTLS secure protocol. |

The following sections provide more details on the NDI modules which are to be deployed at the federated site.

### Directory Services (Replica)
The Directory Services (Replica) is one of the NDI modules which is deployed at the federated site. The following diagram shows a reference deployment configuration at the federated site.

![directory services replica](\assets\lib\aspop\img\directoryservicesreplica.png)

The Directory Services (Replica) consists of the following components:

- NDI User Directory data store – this is essentially a lookup table for form factor related attributes, e.g. form factor type, certificate serial number, status, etc. The ASP use this information during form factor authentication.
- Client Registry data store – this is a lookup table for client app attributes, e.g. client type, client secret, redirect URI, scope, etc.  The ASP use this information to carry out the OAuth 2.0/OIDC authorization flow.
- Extension Registry data store – this is a lookup table for NDI Extension attributes, e.g. extension type, endpoint URI, etc.  The ASP use this information to access extension capabilities e.g. Form Factor Authenticator, through extension interfaces.
- Directory Service API micro-service – this is the API layer which the ASP calls to retrieve form factor, client app and extension data from the data stores.
- Directory Service Replicator micro-service – this is to enable data updates from the Directory Services (Master) to be replicated to the data stores.  It consists of the DS Subscriber API and DS Updater micro-service.  The DS Subscriber should be deployed on a web server or behind an API Gateway in the DMZ of the federated site.  It should be exposed securely through the Internet for the Directory Services (Master) to consume.   Data updates received by the DS Subscriber is updated to the data stores through the DS Updater.
- Data Services micro-service – this is the data layer which abstracts database specific integration from the DS API and DS Updater.
- 
The micro-services may run on any commodity VM or container platform.  The data stores may reside in the federated site’s existing database as long as the database supports standard SQL.  For NoSQL databases, which currently do not have a common standard, it may be necessary to customize the Data Services to integrate with the specific NoSQL database.

### ASP Core 

The following diagram shows a reference deployment configuration of the ASP Core at the federated site.

![asp core](\assets\lib\aspop\img\aspcore.png)

The ASP Core consists of the following components:
- Service Gateway – this is a collection of APIs exposing capabilities of the ASP as follows:
  - Public Interfaces, relying parties accesses the ASP authentication and OIDC capabilities through the ASP API (Public).
  - Private Interfaces, the NDI Login Page and the NDI App (Soft Token) interact with the ASP for authentication through the NDI Login API and Form Factor (Soft Token) Auth API respectively.
- Outbound Gateway -  this provides the path for the ASP to access external capabilities as follows:
  - External OCSP services to obtain the latest certificate status during authentication.
  - CA designated CRL location to periodically downloads the CRL files as fallback for certificate status check in the event of an OCSP outage
  - External Form Factor Authentication Services such as that provided by Mobile Connect for authentication based on other form factors.
- ASP Micro-Services – this is a group of micro-services which constitute the ASP core.
  - OIDC Provider – orchestrates authentication and authorization based on OpenID Connect / OAuth 2.0 flows.  It calls the Directory Services to retrieve form factor attributes to ascertain the type and location of the end-user’s preferred form factor, and the serial number of the certificate associated with it.  It calls the OCSP services to check certificate status and invokes the Form Factor Authentication Services (Soft Token or Biometric) via the Form Factor Authentication Interface to authenticate the end-user with his preferred form factor.  Finally, it interacts with the Authorization Server via the Domain Authorization Interface to obtain the Access Token required to access the target protected domain.  The OIDC Provider module generates the ID Token and calls the Crypto Services to sign it with the ASP private key stored securely in the HSM. 
  - Domain Authorization Interface – this is a standard interface used by the ASP to interface with the Authorization Server of the organization (i.e. the federated site), based on the OAuth 2.0 Token Exchange draft specs.
  - Data Services – this is the data layer which abstracts database/cache specific integration from the other ASP micro-services.   
  - Crypto Services – this is the cryptographic layer which abstracts HSM specific integration from the other ASP micro-services. 
  - Push Notification Interface – this is the abstraction layer which abstracts push notification specific integration from the other ASP micro-services.
  - Form Factor (Soft Token) Authenticator – the abstract layer which abstracts form factor specific integration for authentication from the ASP OIDC Provider micro-service.  The Form Factor Authenticator is an extension service which is a mechanism to let alternative solutions to interoperate with NDI.  NDI provides the Form Factor Authenticator to handle NDI Soft Token form factor authentication.  A NDI-certified form factor provider e.g. Mobile Connect may offer a Form Factor Authenticator for authentication of a user using his crypto-SIM form factor.  The ASP OIDC Provider micro-service will be able to invoke the crypto-SIM Form Factor Authenticator (which may be on-site or remotely hosted) through the same Form Factor Authenticator interface.

The ASP Core needs to integrate to the following on-site modules:
- CRL Batch – this is a batch process to periodically download the CRL files from the CRL distribution point (CDP) of the NDI CA via HTTP.  The CRL files serve as fallback source for certificate status check in the event of an OCSP outage.  The CRL Batch also scans the CRL file belonging to the root CA after every download to ensure the issuing CA certificates are not revoked. 
- Distributed Cache – the distributed cache allows multiple instances of the OIDC Provider micro-service to put and retrieve transient data (e.g. authorization code) during the OIDC flows.  The federated site may use the distributed cache solution which comes with the ASP Core if it does not have an existing distributed cache system.
- HSM (Hardware Security Module) – a minimum FIPS L3 certified hardware based security module to store the ASP private keys for generating signature and random numbers for ID tokens issued by the ASP. 
•	Authorization Server – this is the Identity and Access Management (IAM) system used by the organization to manage and enforce access control policies on its protected resources.   On successful user authentication, the ASP interfaces with the Authorization Server via the Domain Authorization Interface to obtain an access token which is returned to the relying party (client app) for it to access the protected resources subsequently.

### ASP Extensions
The authentication request from the OIDC Provider is forwarded to the target form factor via the Form Factor Authentication Extensions, the default implementations provided are the Soft Token Authenticator and the Biometric Authenticator.  The Soft Token Form Factor Authenticator handles the interactions with the NDI Soft Token form factor, using the Push Notification Interface to send the authentication request to the target form factor in the end-user’s mobile device.  The Biometric Form Factor Authenticator forwards the authentication request to the Biometric Services module of the NDI platform. 

## Deep Integration

This option is suitable for organizations which intend to enhance their existing authentication system to support NDI.

![deep integration](\assets\lib\aspop\img\deepintegration.png)

The organization’s authentication system integrates with the following modules:

|Module| Description |
|------|-------------|
|Directory Services – to obtain the user’s preferred form factor details (e.g. form factor id, type, status, certificate serial, form factor specific attributes, etc.) and the form factor authenticator endpoint URL. |The authentication system integrates with the Directory Services through the Directory Service API.  It calls the Directory Services API to get the user’s form factor record using the user’s NDI id.<br>It then calls the Directory Services API to obtain the endpoint URL of the appropriate Form Factor Authenticator service using the form factor type (ff_type). 
|Form Factor Authenticator – to authenticate the user with his preferred form factor.|The authentication system invokes the Form Factor Authenticator service to perform user authenticator, by calling the Form Factor Authenticator API at the Form Factor Authenticator endpoint.<br><br>The Form Factor Authenticator returns a signed response in the form of a JSON Web Token (JWT) structure, the authentication system must verify the signed response as specified in the Signed Response Verification section.
|OCSP Service – to obtain the current status of the certificate associated with the user’s form factor. | The authentication system invokes the NDI OCSP service using the standard Online Certificate Status Protocol (OCSP) as described in RFC6960, at the NDI OCSP endpoint over HTTPS.<br><br>The authentication system obtains the status of the certificate using the form factor’s certificate serial number (cert_sn), which may be good, revoked or unknown.  The authentication system must check that the certificate status is good. |

# Integration Reference

## Secure Protocol

Integration between the NDI components and the organization’s components is secured by transport level protection and application level authorization.

![secure protocol](\assets\lib\aspop\img\secureprotocol.png)

### Application Level Authorization

NDI ASP modules use access tokens (i.e. Bearer token) to verify access between modules, they support the OAuth 2.0 Client Credentials authorization flow to obtain access tokens from the Authorization Server.  The NDI ASP modules deployed at the federated site may be configured to work with the federated site’s OAuth 2.0 enabled Authorization Server.

### Transport Level Protection
Integration between NDI components and the organization’s components is secured using mutual TLS (mTLS), where both peers (i.e. client and server) of the integration authenticate each other using certificates before establishing the trusted connection.

The federated site is required to generate keystores and CA-signed certificates for NDI modules deployed at the federated site.  These on-site NDI modules may be configured to work with keystores and certificates in popular formats e.g. PEM, DER and supporting the X.509 standard.  Modules running in the same computing node may refer to the same server keystore and certificate generated for the computing node.

The federated site is required to provide the CA-signed certificates and the associated cert chain used by the organization’s components and NDI on-site modules which integrates with the NDI off-site modules (e.g. OCSP, remote hosted Form Factor Authenticator services).

## Domain Authorization Interface

Integration between the ASP and the federated site’s Authorization Server is through the Domain Authorization Interface, which is based on the OAuth 2.0 – Token Exchange draft specifications (draft-ietf-oauth-token-exchange-14). 

This integration is applicable for Mode 1 – ASP as the OIDC Provider, where the ASP calls the federated site’s Authorization Server to obtain an access token, after successfully authenticating the user.  The Authorization Server is to implement the Domain Authorization Interface described as follows: 

### Request

#### POST {basePath}/token

|Parameter|In|Type|Description|
|---------|---------|---------|---------------|
grant_type|Body|String|Must be "urn:ietf:params:oauth:grant-type:token-exchange"|
|subject_token|Body|String|The ID token generated by the ASP after a successful user authentication|
|subject_token_type|Body|String|Must be "urn:ietf:params:oauth:token-type:id_token"|
|scope|Body|String|The scope of access requested, this is a list of space-delimited, case-sensitive values, as defined in RFC6749 (section 3.3).  The values are domain-specific references to resources or services of the protected domain|
|requested_toke_type|Body|String|Set to "urn:ietf:params:oauth:token-type:access_token" to exchange for an access token.|


### Response
Success, the requested access token is returned
HTTP/1.1 200 OK
Content-Type: application/json
{
    "access_token" : "eyJ0eXAiOiJKV1QiLCJhbGciOiJFUzI1NiIsImtpZCI6ImdhbnltZWRlIn0.eyJjbGllbnRfaWQiOiJjb2 1lb25zcHVycyIsImV4cCI6MTIzMjEzNDEyNCwiaWF0IjoyNDEyNDEyMTM3NX0.W_4ViMyL4VhbQRvfLYb2uXSaTmrDWmtd8SGH971ORR6shpWZRoMprzGPcR8VVkwKVYvl0H_8NWSaMudnsGxwFg"
    "issued_token_type" : "urn:ietf:params:oauth:token-type:access_token"
    "token_type" : "Bearer"
    "expires_in" : 600
}
Parameter	In	Type	Description
access_token	Body	String	The access token generated by the Authorization Server, which can be used to access services and resources of the protected domain
issued_token_type	Body	String	The issued token type, which is "urn:ietf:params:oauth:token-type:access_token"
token_type	Body	String	The type of access token returned, which is always "Bearer"
expires_in	Body	String	The lifetime (in seconds) of the access token.  For example, the value "600" denotes the access token will expire in 10 mins from the time the response was generated. 

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

## ASP Authentication API
This API is applicable for federated site operating the ASP in Mode 2 – ASP as an Authenticator, where the federated site’s Authorization Server calls the ASP through the ASP Authentication API for user authentication.

The ASP Authentication API is based on the OpenID Connect Implicit Flow for Authentication Request:

### Request
#### GET {basePath}/asp/auth

Required
Parameter	In	Type	Description
response_type	Body	String	Must be "id_token"
redirect_uri	Body	String	The redirection URI to which the response is sent.  The response will be return directly if this endpoint is called directly by the client
nonce	Body	String	A random generated unique reference used to associate the client session or authentication instance with the ID token to be returned, and to mitigate replay attack.  The value is embedded into the ID token to be returned.

### Response
Success, the requested access token is returned
HTTP/1.1 200 OK
Content-Type: application/json
{
    "id_token" : "eyJ0eXAiOiJKV1QiLCJhbGciOiJFUzI1NiIsImtpZCI6ImdhbnltZWRlIn0.eyJjbGllbnRfaWQiOiJjb2 1lb25zcHVycyIsImV4cCI6MTIzMjEzNDEyNCwiaWF0IjoyNDEyNDEyMTM3NX0.W_4ViMyL4VhbQRvfLYb2uXSaTmrDWmtd8SGH971ORR6shpWZRoMprzGPcR8VVkwKVYvl0H_8NWSaMudnsGxwFg"
    "token_type" : "Bearer"
    "expires_in" : 600
}
Parameter	In	Type	Description
id_token	Body	String	The ID token generated by the ASP after a successful user authentication
token_type	Body	String	The type of access token returned, which is always "Bearer"
expires_in	Body	String	The lifetime (in seconds) of the access token.  For example, the value "600" denotes the access token will expire in 10 mins from the time the response was generated. 

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

## Directory Services API
For organizations which choose the Deep Integration federation option, the organization’s authentication system integrates with the Directory Services to obtain form factor attributes and extension endpoint info. 

### Get Form Factor Attributes

The Directory Services consists of the NDI User Directory data store which holds the form factor records of NDI users.  When the user logins using NDI, the organization’s authentication system fetches his form factor record from the NDI User Directory using his login id (i.e. his NDI Id).  

The form factor record has the following attributes:

|Attribute|Field Name|Description|
|---------|---------|---------|
|User UUID|uuid|A unique identifier used by NDI to reference the end-user.  This is the key user reference which will be provided to the client apps (relying parties) by the ASP after successful authentication.  The UUID is a perpetual id that remains constant throughout the lifespan of the end-user account.|
|NDI Id|ndi_id|A unique id personalized by the end-user, this is the user id which the end-user enters into the NDI Login Page.  The end-user can have only one personalized NDI Id, though he may change it from time to time, as long as the value is already taken by other end-users.  This is stored as a hash value (SHA-256).|
|Certificate Serial Number|cert_sn|The serial number of the digital certificate issued to the end-user for this form factor.|
|Status|status|The current status of this particular form factor – Active / Suspended / Revoked / Dormant|
|Default Indicator	preferred	This is a flag indicating this is the end-user’s preferred form factor for authentication and online transactions.
Form Factor Type	ff_type	This is a value identifying the form factor type.
Form Factor Host	ff_host	A reference to the device hosting this form factor.
Form Factor Id	ff_id	This is an unique identifier of the form factor, specific to the form factor type.
Used For	used_for	The permitted usage of the form factor (e.g. authentication, signing)
IAL	ial	The identity assurance level (IAL) achieved for this form factor.
IA Method	ia_meth	This is a list of references of the identity assurance methods performed on this form factor.
Form Factor Attributes	ff_attr	This is identity provider/form factor-specific, containing a list of attributes in a JSON (name-value) structure.   For NDI soft token, this list includes attributes like device push notification registration id, token id, etc.  For mobile-SIM based form factor, this list may include MSISDN, subscriber MNO, etc.  The ASP uses these attributes to identify the location of the form factor in order to route the authentication request.

The organization’s authentication system uses the form factor record for the following:
Form Factor Status (status)	Check the status of the form factor, reject the login if the status is not “active”
Form Factor Type (ff_type)	Determine the Form Factor Authenticator endpoint to invoke, this is done by fetching the endpoint info from the Endpoint Registry using this value
Form Factor Attributes (ff_attr)	Provide this value as part of the inputs of the authentication request when invoking the Form Factor Authenticator
Certificate Serial Number (cert_sn)	Check that the certificate returned in the signed response has the same serial number as this value, to ensure the certificate genuinely belongs to the user

#### Request
GET {basePath}/directory/users/{ndi_id}/formfactors
Required
Parameter	In	Type	Description
ndi_id	Path	String	The user NDI id, given in its hash value (SHA-256, hex format)
preferred	Query	String	If provided, only the user’s preferred form factor record is returned
cert_sn	Query	String	If provided, only the user’s form factor with the specified certificate serial number is returned.  This is ignored if the preferred parameter is specified.

#### Response
Success, the requested form factor record is returned
HTTP/1.1 200 OK
Content-Type: application/json
{
  [{"ndi_id":"ea7226bf8f01cfea674180de5dae8123b00361a3961ffa9cb4107761f23315ad",
    "uuid":"ac380d80-8fb4-11e8-bfc6-17bd42cfa150",
    "cert_sn":"1532486681724 (0x164cf53047c)",
    "ff_id":"ad39c610-8fb4-11e8-ac49-9998bae12994",
    "ff_type":"stoken",
    "ff_host":"j10",
    "status":"active",
    "preferred":"y",
    "use_for":"authn",
    "ial":"1",
    "ia_meth":"[\"demoia\"]",
    "ff_attr":"{\"mp_fcm_token\":\"ekdj4S7SaG4:APA91bFQ6vWaTQWl1i3_XPfLTcPSi4MijrZ6HAVw-767PHspaAaKxNOArJfkfeQB_5bahdIQZ1JIF3jm1SAfPO3A9Ck-qnlanbEc2FjPqxVMreDrT3IDcRZjmHnfRq7Hi8ZKpWkCjITUqCSPds7GLRTfVGc8BmAMSQ\"}", "req_by":"undefined","created_t":"2018-07-25T02:44:38.000Z","updated_t":"2018-07-25T02:44:45.000Z"}]
}
Parameter	In	Type	Description
ndi_id	Body	String	The user NDI id, in its hash value (SHA-256 hex)
uuid	Body	String	The user UUID
cert_sn	Body	String	The certificate serial number of the certificate issued to this form factor
ff_id	Body	String	The form factor id
ff_type	Body	String	The form factor type
ff_host	Body	String	The reference to the host device hosting the form factor
status	Body	String	The form factor status – “active”, “suspended”, “revoked”, or “dormant”
preferred	Body	String	“y” – this is user’s preferred form factor 
used_for	Body	String	The intended usage of this form factor – “authn” or “signing”
ial	Body	String	The identity assurance level – “1”, “2” or “3”
ia_meth	Body	String	A space delimited string containing the identity methods performed on this form factor
ff_attr	Body	String	A JSON object containing other attributes which are specific to the form factor type, e.g. the FCM push notification registration token.

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

### Get Extension Endpoint Info
The Directory Services consists of the Extension Registry data store which holds the endpoint info of extension services available to NDI.  When the user logins using NDI, the organization’s authentication system fetches his form factor record from the NDI User Directory using his login id (i.e. his NDI Id).  

The form factor record has the following attributes:

|Attribute|	Field Name|	Description|
|---------|---------|---------|
Extension Type	ext_type	The extension type.  There are a variety of extension services catering to different aspects of the NDI platform, e.g. Form Factor Authenticator for form factor authentication, Form Factor Provisioning for provisioning new form factors, etc.  This attribute let the ASP select the appropriate extension service for the task it is currently performing.
Extension Endpoint URI	endpt_uri	The endpoint URI to invoke the extension service.  The extension is a service which implements a NDI-defined extension interface (i.e. a set of API definition) and exposes the API at the extension endpoint URI for the ASP to consume.
Form Factor Type	ff_type	The form factor type.  Extension services are usually form factor specific, this attribute let the ASP select the extension service which is appropriate for the form factor it is currently dealing with.

After obtaining the form factor record, the organization’s authentication system fetches the extension endpoint URI of the appropriate Form Factor Authenticator from the Extension Registry of the Directory Services.

#### Request
##### GET {basePath}/directory/extensions/{type}
Required
Parameter	In	Type	Description
type	Path	String	The extension type
Ff_type	Query	String	The form factor type

#### Response
Success, the requested access token is returned
HTTP/1.1 200 OK
Content-Type: application/json
{
    "endpint_uri" : "https://www.sfffauth.com"
    "alt_endpoint_uri" : "https://www.sfffauth2.com"
}
Parameter	In	Type	Description
endpoint_uri	Body	String	The extension endpoint URI

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

## Signed Response Verification

The Signed Response from the end-user’s form factor is a JSON Web Signature (JWS) consisting of the following parameters.  The following checks are to be performed to verify the authentication.

|Parameter|Description|Checks to Perform|
|---------|---------|---------|
JOSE Header
typ	“JWT”	
alg	Signature algorithm – “ES256”	Must be “ES256” – ECC P256
x5c	The X.509 certificate corresponding to the key used to sign the JWS, in base64-coded DER format	Certificate must be owned by the end-user.  The certificate serial number must match the certificate serial number in the form factor attributes retrieved from the Directory Services.
		Certificate must not be expired.  Check the validity period field in the certificate against system time.
		Certificate must be valid.  Verify the certificate chain using the NDI root CA and issuing CA certificates.
JWS Payload
client_id	Echoes the client_id parameter in the signed challenge	Must match the client_id (cached) of the authentication request sent to the form factor.
usr_req	Echoes the usr_req parameter in the signed challenge	Must match the usr_req (cached) of the authentication request sent to the form factor.
usr_resp	This indicates the choice made by the end-user in response to the authentication request – may be “accept”, “consent”, or “reject”	Must be “accept” or “consent”
token_sn	The unique token serial number assigned to the soft token of the end-user	Must match the token serial number in the form factor attributes retrieved from the Directory Services.
sign_time	The timestamp of the signature, in POSIX time	Must be within the permitted time tolerance defined by the ASP or organization’s authentication system.
challenge	The random generated challenge string sent in the authentication request	Must match the challenge string (cached) of the authentication request sent to the form factor.
JWS Signature
signature	This is the digital signature over the JOSE Header and JWS Payload, using the algorithm specified in the alg Header parameter	Signature created with the end-user’s private key.  Must be valid.  Verify the signature using the certificate specified in the x5c Header parameter

# Failover Considerations

This section describes the recovery action which may be taken in the event of the following outages.

## OCSP
The ASP calls the OCSP service to check certificate status during user authentication.  In the event the OCSP service is not available, the federated site may activate the following fallback mechanisms:

### Verification by CRL
The ASP may be configured to switch to verifying certificate status using the local copy of the CRL.  The default trigger for the switch is based on the number of timeout errors encountered calling the OCSP service, the trigger value may be set in the ASP configuration.  Alternatively, the switch may be triggered by an external event sent by the federated site’s monitoring system.

As the CRL may not contain the latest certificate revocation status, the federated site has to put in place risk assessment to determine the type of transactions to allow during this fallback state.

### Verification by Form Factor Status

The form factor record fetched from the Directory Services contains the form factor status which closely reflects the status of its associated certificate.  In most situations, the revocation of certificates is done through the RA API, which will update the form factor status of the associated form factor record in the Directory Services (master) accordingly to “revoked”.  The updated form factor status is then propagated to the Directory Services replicas at federated sites.

The ASP checks the form factor status to ensure it is “active” before proceeding with user authentication.  For an organization which uses the Deep Integration federation option, the organization’s authentication system will have to perform the form factor status check. 

As there is a small latency in propagation of updates to the Directory Services replicas, the form factor status may not reflect the latest certificate revocation status of its associated certificate.  The federated site has to put in place risk management mechanism to determine the type of transactions to allow during this fallback state.

## Form Factor Authenticator

This scenario applies to remotely hosted Form Factor Authenticator services.  In the event of the remotely hosted Form Factor Authenticator service is not available, the ASP will switch to use the alternate endpoint URI to invoke the alternate instance of the Authenticator service.  The default trigger for the switch is based on the number of timeout errors encountered calling the Authenticator service, the trigger value may be set in the ASP configuration.  Alternatively, the switch may be triggered by an external event sent by the federated site’s monitoring system.

For an organization which uses the Deep Integration federation option, the organization’s authentication system will have to switch to use the alternate endpoint URI to invoke the alternate instance of the Form Factor Authenticator service. 

## Directory Services

The Directory Services are typically deployed on-site at the federated site in the form of the Directory Service Replica.  If the on-site Directory Service Replica failed, it is usually due to factors within the federated site, the resiliency measure and recovery action should be part of the operation practices of the federated site.
The Directory Services Replica has a cache mechanism (DS cache) which can be enabled to cache a configurable amount of frequently accessed form factor records into a distributed in-memory data cache.  In the event of a database-related outage, the DS cache will continue to function and let the ASP fetch cached form factor records.

### Directory Services Replication Failure

In the event of the Directory Service replication service failure, updates will not be propagated to the Directory Services Replicas at federated sites.  New users onboarded to NDI during the outage will not be able to login using NDI at federated sites.  The federated site may continue to rely on its Directory Services replica for existing NDI users as long as the OCSP service is available to provide the latest certificate revocation status.

 
