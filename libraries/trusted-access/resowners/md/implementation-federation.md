### Federation Approach
<br/>

These are the 3 approaches which can be taken to setup a federated ASP:

+ Cloud ASP Service
+ Federated ASP Pack
+ Deep Integration

#### Cloud ASP Service
<br/>

This is the simplest option for federation. The ASP is available as a cloud service, and a dedicated Cloud ASP Service instance may be created for the organization which will operate it.

![Cloud ASP Flow](/assets/lib/trusted-access/resowners/img/cloudaspflow.png)

The Cloud ASP Service integrates with the following on-site modules:

<table>
<thead>
<tr class="header">
<th>Modules</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>Authorization Server of the organization.</p>
<p>This is the module used by the organization to manage and enforce access control policies on its protected resources. An IAM product may be used to fulfil this role.</p></td>
<td><p>For Mode 1 – ASP as the OIDC Provider the Cloud ASP Service initiates the call to the Authorization Server for token exchange. The Authorization Server will integrate with the ASP through the Domain Authorization Interface.</p>
<p>For Mode 2 – ASP as an Authenticator, the Authorization Server initiates the call to the Cloud ASP Service to perform user authentication. The Authorization Server will integrate with the ASP through the ASP Authentication API.</p>
<p>Integration requirements:</p>
<ul>
<li><p>Internet access of appropriate bandwidth for integration between ASP and the Authorization Server</p></li>
<li><p>Certificate-based mTLS secure protocol between ASP and Authorization Server.</p></li>
</ul></td>
</tr>
<tr class="even">
<td>Relying Parties (i.e. client apps) accessing the protected resources of the organization.</td>
<td><p>This is only applicable for Mode 1 – ASP as the OIDC Provider. The organization’s relying parties integrate with the ASP through the ASP OpenID Connect interface.</p>
<p>Integration requirements:</p>
<ul>
<li><p>Internet access of appropriate bandwidth for integration between relying parties and ASP</p></li>
<li><p>Certificate-based mTLS secure protocol between relying parties and ASP.</p></li>
</ul></td>
</tr>
</tbody>
</table>

#### Federated ASP Pack

This option is for organizations which have on-premises requirement. Obtain and deploy pre-built ASP modules at the federated site. The deployment of the pre-built ASP pack at the federated site is made relatively easy by support for Open Standards and commodity infrastructure. The deployment steps are largely automated through deployment scripts.

##### Deployment Pre-requisites

These are the pre-requisites for Federated ASP Pack deployment to the federated site:

<table>
<thead>
<tr class="header">
<th>Components</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Servers – to run the ASP and DS micro-services</td>
<td><p>These may be commodity VMs running a Linux based OS (RedHat Enterprise Linux recommended). The ASP and DS micro-services support load-balancing and horizontal scaling based on virtual IP address.</p>
<p>A container version of the ASP Core and DS Replica is available to run on PaaS / container platform which supports Docker.</p></td>
</tr>
<tr class="even">
<td>Database – to host the DS data stores</td>
<td><p>These may be any SQL database (MySQL Enterprise recommended).</p>
<p>If your existing database is NoSQL, the DS Data Services micro-service will have to be customized to integrate with your NoSQL, as there is currently no industry standards for accessing NoSQL databases.</p></td>
</tr>
<tr class="odd">
<td>Hardware Secure Module (HSM) – to store ASP keys</td>
<td><p>These may be any FIPS L3 certificate HSM which supports PKCS#11.</p>
<p>If your HSM does not support PKCS#11, the ASP Crypto Services micro-service will have to be customized to integrate with your HSM using proprietary interfaces.</p></td>
</tr>
<tr class="even">
<td>Distributed Cache – to store transient data shared by the ASP micro-service instances</td>
<td><p>These may be any distributed cache which supports the JCache interface (JSR107).</p>
<p>If your distributed cache does not support JCache, the ASP Data Services micro-service will have to be customized to integrate with your distributed cache system. Alternatively, you may use the distributed cache solution of the ASP Core package.</p></td>
</tr>
</tbody>
</table>

##### Deployment Overview

The following diagram shows an overview of a Federated ASP Pack deployment with integration points to on-site and off-site components.

![Deployment Overview Flow](/assets/lib/trusted-access/resowners/img/deploymentoverview.png)

The NDI modules to be deployed in the federated site are:
+ ASP Core – comprising a group of micro-services providing the core features of the ASP
+ Directory Services (Replica) – comprising the NDI User Directory and the Client Registry data stores

The ASP integrates with the following on-site modules, integration approach to these on-site modules will depend on the operating mode which the organization use to run the ASP.

<table>
<thead>
<tr class="header">
<th>Module</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>Authorization Server of the organization.</p>
<p>This is the module used by the organization to manage and enforce access control policies on its protected resources. An IAM product may be used to fulfil this role.</p></td>
<td><p>For Mode 1 – ASP as the OIDC Provider, the ASP will initiate the call to the Authorization Server for token exchange. The Authorization Server will integrate with the ASP through the Domain Authorization Interface.</p>
<p>For Mode 2 – ASP as an Authenticator, the Authorization Server will be initiating a call to the ASP to perform user authentication. The Authorization Server will integrate with the ASP through the ASP Authentication API.</p>
<p>Integration requirements:</p>
<ul>
<li><p>Internet access of appropriate bandwidth for integration between ASP and the Authorization Server</p></li>
<li><p>Certificate-based mTLS secure protocol between ASP and Authorization Server.</p></li>
</ul></td>
</tr>
<tr class="even">
<td>Relying Parties (i.e. client apps) accessing protected resources of the organization.</td>
<td><p>This is only applicable for Mode 1 – ASP as the OIDC Provider. The organization’s relying parties integrate with the ASP through the ASP OpenID Connect interface.</p>
<p>Integration requirements:</p>
<ul>
<li><p>Internet access of appropriate bandwidth for integration between relying parties and ASP</p></li>
<li><p>Certificate-based mTLS secure protocol between relying parties and ASP.</p></li>
</ul></td>
</tr>
</tbody>
</table>


The ASP integrates with the following off-site modules:

<table>
<thead>
<tr class="header">
<th>Module</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Directory Services (Master) – to enable replication of data to the Directory Service Replica on the federated site.</td>
<td>Federated site to allow HTTPS access over Internet for the Directory Service (Master) to call the DS Subscriber, through certificate-based mTLS secure protocol.</td>
</tr>
<tr class="even">
<td>OCSP (Online Certificate Status Protocol) service – to obtain certificate status during user authentication.</td>
<td>Federated site to allow HTTPS access over Internet for the ASP OIDC Services to call the NDI OCSP service, through certificate-based mTLS secure protocol.</td>
</tr>
<tr class="odd">
<td>CA CRL distribution point – to download CRL file as a fallback for certificate status check when the OCSP service is unavailable.</td>
<td>Federated site to download the CRL file periodically from the NDI CA CRL distribution point. This may be done with HTTP or FTP. The CRL files to be downloaded to a location accessible by the ASP OIDC Services module.</td>
</tr>
<tr class="even">
<td>Form Factor Authenticator – to initiate user authentication with the user’s preferred form factor. The ASP comes with a built-in Form Factor Authenticator for the NDI soft token form factor. However, if the user’s preferred form factor is an alternative form factor (e.g. crypto-SIM), the ASP will need to route the authentication request to the corresponding Form Factor Authenticator which may be hosted remotely.</td>
<td>Federated site to allow HTTPS access over Internet for the ASP OIDC Services to call the remotely hosted Form Factor Authenticator services of available alternative form factors (e.g. crypto-SIM), through certificate-based mTLS secure protocol.</td>
</tr>
</tbody>
</table>

The following sections provide more details on the NDI modules which are to be deployed at the federated site.

##### Directory Services (Replica)

The Directory Services (Replica) is one of the NDI modules which is deployed at the federated site. The following diagram shows a reference deployment configuration at the federated site.

![Deployment Overview Flow](/assets/lib/trusted-access/resowners/img/directoryservicereplica.png)

The Directory Services (Replica) consists of the following components:

+ NDI User Directory data store – this is essentially a lookup table for form factor related attributes, e.g. form factor type, certificate serial number, status, etc. The ASP use this information during form factor authentication.
+ Client Registry data store – this is a lookup table for client app attributes, e.g. client type, client secret, redirect URI, scope, etc. The ASP use this information to carry out the OAuth 2.0/OIDC authorization flow. 
+ Extension Registry data store – this is a lookup table for NDI Extension attributes, e.g. extension type, endpoint URI, etc. The ASP use this information to access extension capabilities e.g. Form Factor Authenticator, through extension interfaces.
+ Directory Service API micro-service – this is the API layer which the ASP calls to retrieve form factor, client app and extension data from the data stores.
+ Directory Service Replicator micro-service – this is to enable data updates from the Directory Services (Master) to be replicated to the data stores. It consists of the DS Subscriber API and DS Updater micro-service. The DS Subscriber should be deployed on a web server or behind an API Gateway in the DMZ of the federated site. It should be exposed securely through the Internet for the Directory Services (Master) to consume. Data updates received by the DS Subscriber is updated to the data stores through the DS Updater.
+ Data Services micro-service – this is the data layer which abstracts database specific integration from the DS API and DS Updater.

The micro-services may run on any commodity VM or container platform. The data stores may reside in the federated site’s existing database as long as the database supports standard SQL. For NoSQL databases,which currently do not have a common standard, it may be necessary to customize the Data Services to integrate with the specific NoSQL database.

##### ASP Core

The following diagram shows a reference deployment configuration of the ASP Core at the federated site.

![Deployment Overview Flow](/assets/lib/trusted-access/resowners/img/aspcoreflow.png)

The ASP Core consists of the following components:

+ Service Gateway – this is a collection of APIs exposing capabilities of the ASP as follows:
    
    + Public Interfaces, relying parties accesses the ASP  authentication and OIDC capabilities through the ASP API (Public).
    
    + Private Interfaces, the NDI Login Page and the NDI App (Soft Token) interact with the ASP for authentication through the NDI Login API and Form Factor (Soft Token) Auth API respectively.

+ Outbound Gateway - this provides the path for the ASP to access external capabilities as follows:

    + External OCSP services to obtain the latest certificate status during authentication.

    + CA designated CRL location to periodically downloads the CRL files as fallback for certificate status check in the event of an OCSP outage

    + External Form Factor Authentication Services such as that provided by Mobile Connect for authentication based on other form factors.

+ ASP Micro-Services – this is a group of micro-services which constitute the ASP core.

    + OIDC Provider – orchestrates authentication and authorization based on OpenID Connect / OAuth 2.0 flows. It calls the Directory Services to retrieve form factor attributes to ascertain the type and location of the end-user’s preferred form factor, and the serial number of the certificate associated with it. It calls the OCSP services to check certificate status and invokes the Form Factor Authentication Services (Soft Token or Biometric) via the Form Factor Authentication Interface to authenticate the end-user with his preferred form factor. Finally, it interacts with the Authorization Server via the Domain Authorization Interface to obtain the Access Token required to access the target protected domain. The OIDC Provider module generates the ID Token and calls the Crypto Services to sign it with the ASP private key stored securely in the HSM.

    + Domain Authorization Interface – this is a standard interface used by the ASP to interface with the Authorization Server of the organization (i.e. the federated site), based on the OAuth 2.0 Token Exchange draft specs. 

    + Data Services – this is the data layer which abstracts database/cache specific integration from the other ASP micro-services.

    + Crypto Services – this is the cryptographic layer which abstracts HSM specific integration from the other ASP micro-services.

    + Push Notification Interface – this is the abstraction layer which abstracts push notification specific integration from the other ASP micro-services. 

    + Form Factor (Soft Token) Authenticator – the abstract layer which abstracts form factor specific integration for authentication from the ASP OIDC Provider micro-service. The Form Factor Authenticator is an *extension* service which is a mechanism to let alternative solutions to interoperate with NDI. NDI provides the Form Factor Authenticator to handle NDI Soft Token form factor authentication. A NDI-certified form factor provider e.g. Mobile Connect may offer a Form Factor Authenticator for authentication of a user using his crypto-SIM form factor. The ASP OIDC Provider micro-service will be able to invoke the crypto-SIM Form Factor Authenticator (which may be on-site or remotely hosted) through the same Form Factor Authenticator interface.

The ASP Core needs to integrate to the following on-site modules:

+ CRL Batch – this is a batch process to periodically download the CRL files from the CRL distribution point (CDP) of the NDI CA via HTTP. The CRL files serve as fallback source for certificate status check in the event of an OCSP outage. The CRL Batch also scans the CRL file belonging to the root CA after every download to ensure the issuing CA certificates are not revoked.

+ Distributed Cache – the distributed cache allows multiple instances of the OIDC Provider micro-service to put and retrieve transient data (e.g. authorization code) during the OIDC flows. The federated site may use the distributed cache solution which comes with the ASP Core if it does not have an existing distributed cache system.

+ HSM (Hardware Security Module) – a minimum FIPS L3 certified hardware based security module to store the ASP private keys for generating signature and random numbers for ID tokens issued by the ASP.

+ Authorization Server – this is the Identity and Access Management (IAM) system used by the organization to manage and enforce access control policies on its protected resources. On successful user authentication, the ASP interfaces with the Authorization Server via the Domain Authorization Interface to obtain an access token which is returned to the relying party (client app) for it to access the protected resources subsequently.

######  ASP Extensions
<br/>

The authentication request from the OIDC Provider is forwarded to the target form factor via the Form Factor Authentication Extensions, the default implementations provided are the Soft Token Authenticator and the Biometric Authenticator. The Soft Token Form Factor Authenticator handles the interactions with the NDI Soft Token form factor, using the Push Notification Interface to send the authentication request to the target form factor in the end-user’s mobile device. The Biometric Form Factor Authenticator forwards the authentication request to the Biometric Services module of the NDI platform.

#### Deep Integration
<br/>

This option is suitable for organizations which intend to enhance their existing authentication system to support NDI.

The organization’s authentication system integrates with the following modules:

<table>
<thead>
<tr class="header">
<th>Module</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Directory Services – to obtain the user’s preferred form factor details (e.g. form factor id, type, status, certificate serial, form factor specific attributes, etc.) and the form factor authenticator endpoint URL.</td>
<td><p>The authentication system integrates with the Directory Services through the Directory Service API. It calls the Directory Services API to get the user’s form factor record using the user’s NDI id.</p>
<p>It then calls the Directory Services API to obtain the endpoint URL of the appropriate Form Factor Authenticator service using the form factor type (ff_type).</p></td>
</tr>
<tr class="even">
<td>Form Factor Authenticator – to authenticate the user with his preferred form factor.</td>
<td><p>The authentication system invokes the Form Factor Authenticator service to perform user authenticator, by calling the Form Factor Authenticator API at the Form Factor Authenticator endpoint.</p>
<p>The Form Factor Authenticator returns a signed response in the form of a JSON Web Token (JWT) structure, the authentication system must verify the signed response as specified in the Signed Response Verification section.</p></td>
</tr>
<tr class="odd">
<td>OCSP Service – to obtain the current status of the certificate associated with the user’s form factor.</td>
<td><p>The authentication system invokes the NDI OCSP service using the standard Online Certificate Status Protocol (OCSP) as described in RFC6960, at the NDI OCSP endpoint over HTTPS.</p>
<p>The authentication system obtains the status of the certificate using the form factor’s certificate serial number (cert_sn), which may be good, revoked or unknown. The authentication system must check that the certificate status is good.</p></td>
</tr>
</tbody>
</table>
