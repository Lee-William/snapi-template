### Introduction 

<br>

The Registration Authority (RA) is an entity trusted by the national CA to ensure an identity is verified to be genuine and properly provisioned before a digital certificate is issued to the identity.  This would typically involve the following activities:
-	Identity assurance, to verify the user’s identity before issuing them a digital certificate 
-	Digital identity provisioning, the process of generating the cryptographic key-pair for the end-user, and preparing the form factor to securely store the key-pair and the certificates issued.  The RA does not actually perform this function, it may trigger and provide the identity info required by the appropriate form factor provisioning process. 
Another important function of the RA is being the touchpoint for the certificate lifecycle management services of the CA, for end-users to manage their certificates, such as performing renewal or revocation. 
The segregation of RA from the national CA allows certain RA functions to be federated and run by certified third parties other than the State.  It allows NDI-certified form factor providers to incorporate the NDI user registration as/into their customer on-boarding process.  For example, Telco’s offering Mobile Connect services may operate a federated provisioning function for their crypto-SIM form factor, and link to the State-run RA identity assurance function (e.g. SingPass 2FA, Passport Biometric) to verify and register their subscribers to both Mobile Connect and NDI.
As with the CA, the RA function consists of both the technical and process aspects.  This document focuses on the technical aspect and the RA interfaces to the other parts of the NDI Platform.
 
The above diagram shows the typical RA configuration and the user registration flow.

![Form Factor Authentication Flow](/assets/lib/ndi/secra/img/ra_flow.png)


