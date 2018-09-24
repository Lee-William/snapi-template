### Introduction
<br>


A key ecosystem partner is the form factor solution providers.  A form factor solution provider planning to introduce a NDI-compliant form factor alternative (e.g. mobile SIM, eSIM, smartcard) has to provide the following Extension implementations:
-	Form Factor Provisioning Service – this is called by the RA during user registration to provision digital identity to the form factor supplied by the provider.
-	Form Factor Authentication Service – this is called by the ASP to interact with the trusted local agent at the form factor to perform the PKI-based user authentication.  
-	Form Factor – this includes the software/hardware (e.g. crypto-SIM) required to secure the form factor on the host device and the trusted local agent to interact with its corresponding Form Factor Authentication Service and perform PKI operations for authentication and signing.

The diagram shows an example of what an alternative form factor solution provider needs to put in place:
-	Form Factor Provisioning Extension – the adapter implementing the Extension Interface and the system consisting of the hardware/software modules and infrastructure required to perform provisioning of the form factor
-	Form Factor Authentication Extension – the adapter implementing the Extension Interface and the system consisting of the hardware/software modules and infrastructure required to perform the last mile authentication with the form factor
-	Form Factor – the form factor solution consisting of the hardware/software modules that run on the host device and the trusted local agent which interacts with the Form Factor Authentication Service.

![Form Factor Authentication Flow](\assets\lib\ffprov\img\ff_flow.png)

<br>

### Contact Us 
<br>

Reach out to us at support@ndi.gov.sg if you are keen to contribute to the NDI ecosystem as a form factor provider.  