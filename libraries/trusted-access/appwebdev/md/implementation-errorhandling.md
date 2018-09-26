# Components

## Directory Services Replica

The Directory Services (Replica) is one of the NDI modules which is deployed at the federated site. The following diagram shows a reference deployment configuration at the federated site.

![Directory Service Replica](/assets/lib/trusted-access/appwebdev/img/dsreplica.png)

The Directory Services (Replica) consists of the following components:

+ NDI User Directory data store – this is essentially a lookup table for form factor related attributes, e.g. form factor type, certificate serial number, status, etc.   The ASP use this information during form factor authentication.

+ Client Registry data store – this is a lookup table for client app attributes, e.g. client type, client secret, redirect uri, scope, etc.  The ASP use this information to carry out the OAuth 2.0/OIDC authorization flow.

+ Extension Registry data store – this is a lookup table for NDI Extension attributes, e.g. extension type, endpoint uri, etc.  The ASP use this information to access extension capabilities e.g. Form Factor Authenticator, through extension interfaces.

+ Directory Service API micro-service – this is the API layer which the ASP calls to retrieve form factor, client app and extension data from the data stores.

+ Directory Service Replicator micro-service – this is to enable data updates from the Directory Services (Master) to be replicated to the data stores.  It consists of the DS Subscriber API and DS Updater micro-service.  The DS Subscriber should be deployed on a web server or behind an API Gateway in the DMZ of the federated site.  It should be exposed securely through the Internet for the Directory Services (Master) to consume.   Data updates received by the DS Subscriber is updated to the data stores through the DS Updater.

+ Data Services micro-service – this is the data layer which abstracts database specific integration from the DS API and DS Updater.

The micro-services may run on any commodity VM or container platform.  The data stores may reside in the federated site’s existing database as long as the database supports standard SQL.  For NoSQL databases, which currently do not have a common standard, it may be necessary to customize the Data Services to integrate with the specific NoSQL database.

## ASP Core

The following diagram shows a reference deployment configuration of the ASP Core at the federated site.

![ASP Core](/assets/lib/trusted-access/appwebdev/img/aspcore.png)

The ASP Core consists of the following components:

+ Service Gateway – this is a collection of APIs exposing capabilities of the ASP as follows:

  + Public Interfaces, relying parties accesses the ASP authentication and OIDC capabilities through the ASP API (Public).

  + Private Interfaces, the NDI Login Page and the NDI App (Soft Token) interact with the ASP for authentication through the NDI Login API and Form Factor (Soft Token) Auth API respectively.

+ Outbound Gateway -  this provides the path for the ASP to access external capabilities as follows:

  + External OCSP services to obtain the latest certificate status during authentication.

  + CA designated CRL location to periodically downloads the CRL files as fallback for certificate status check in the event of an OCSP outage

  + External Form Factor Authentication Services such as that provided by Mobile Connect for authentication based on other form factors.

+ ASP Micro-Services – this is a group of micro-services which constitute the ASP core.

  + OIDC Provider – orchestrates authentication and authorization based on OpenID Connect / OAuth 2.0 flows.  It calls the Directory Services to retrieve form factor attributes to ascertain the type and location of the end-user’s preferred form factor, and the serial number of the certificate associated with it.  It calls the OCSP services to check certificate status and invokes the Form Factor Authentication Services (Soft Token or Biometric) via the Form Factor Authentication Interface to authenticate the end-user with his preferred form factor.  Finally, it interacts with the Authorization Server via the Domain Authorization Interface to obtain the Access Token required to access the target protected domain.  The OIDC Provider module generates the ID Token and calls the Crypto Services to sign it with the ASP private key stored securely in the HSM.

  + Domain Authorization Interface – this is a standard interface used by the ASP to interface with the Authorization Server of the organization (i.e. the federated site), based on the OAuth 2.0 Token Exchange draft specs.

  + Data Services – this is the data layer which abstracts database/cache specific integration from the other ASP micro-services.

  + Crypto Services – this is the cryptographic layer which abstracts HSM specific integration from the other ASP micro-services.

  + Push Notification Interface – this is the abstraction layer which abstracts push notification specific integration from the other ASP micro-services.

  + Form Factor (Soft Token) Authenticator – the abstract layer which abstracts form factor specific integration for authentication from the OIDC Provider micro-service.

The ASP Core needs to integrate to the following on-site modules:

 + CRL Batch – this is a batch process to periodically download the CRL files from the CRL distribution point (CDP) of the NDI CA via HTTP.  The CRL files serve as fallback source for certificate status check in the event of an OCSP outage.  The CRL Batch also scans the CRL file belonging to the root CA after every download to ensure the issuing CA certificates are not revoked.

 + Distributed Cache – the distributed cache allows multiple instances of the OIDC Provider micro-service to put and retrieve transient data (e.g. authorization code) during the OIDC flows.  The federated site may use the distributed cache solution which comes with the ASP Core if it does not have an existing distributed cache system.

  + HSM (Hardware Security Module) – a minimum FIPS L3 certified hardware based security module to store the ASP private keys for generating signature and random numbers for ID tokens issued by the ASP.

  + Authorization Server – this is the Identity and Access Management (IAM) system used by the organization to manage and enforce access control policies on its protected resources.   On successful user authentication, the ASP interfaces with the Authorization Server via the Domain Authorization Interface to obtain an access token which is returned to the relying party (client app) for it to access the protected resources subsequently.

## ASP Extensions

The authentication request from the OIDC Provider is forwarded to the target form factor via the Form Factor Authentication Extensions, the default implementations provided are the Soft Token Authenticator and the Biometric Authenticator.  The Soft Token Form Factor Authenticator handles the interactions with the NDI Soft Token form factor, using the Push Notification Interface to send the authentication request to the target form factor in the end-user’s mobile device.  The Biometric Form Factor Authenticator forwards the authentication request to the Biometric Services module of the NDI platform.
