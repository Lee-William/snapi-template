### Step by Step Guide
<br/>

#### Configure Sandbox
<br/>

The NDI Sandbox is a full-featured system (albeit much diminished in scale and resiliency compared to the actual system) showcasing the key use cases and flows of NDI.  It serves as a playground for developers to experiment with the API to quickly gain an understanding of how NDI works.  Follow these steps to start your exploration.

##### Create Test Digital Identity
<br/>

The NDI Sandbox provides the DemoKeys app which let you enrol test users to NDI Sandbox and create digital identities for them.  Download and install the respective DemoKeys app from https://sandbox.demo.ndi.gov.sg for your phone or follow the steps to create test user ids which you can use to try out the ASP API.

1. Download and install the Sandbox Mobile App from:
   + Android users click [here](https://bit.ly/2NH3loL)  
   + IOS Users click [here](https://bit.ly/2QyitmR) (_once downloaded, do trust the certificate from “Government Technology Agency” in your [Profiles & Device Management] setting._)

2. Follow steps listed below to start registration process:
   ![Enrolment Flow](/assets/lib/trusted-access/appwebdev/img/enrolmentflow.png)

##### Try Out NDI Login
<br/>

You should use the Demo-WebApp application provided by the Sandbox to ensure the test users you created are working.

1.	Open browser on your mobile device to https://sandbox.demo.ndi.gov.sg/demowapp/home
2.	Click Try Out NDI Login, the Demo-WebApp screen appears, click NDI Login to launch the NDI Login screen:
![NDI Login Screen](/assets/lib/trusted-access/appwebdev/img/ndiloginscreen.png)
3.	Enter the test user id and click Login.
4.	You should receive a push notification on your device informing you of an authentication request. Clicking on the notification opens the DemoKeys Form Factor screen to let you enter the PIN to unlock the digital key of the test user.

#### Create Client Credentials for your applications
<br/>

You will need client credentials for your Web/Mobile App to perform authentication and authorization with Sandbox API.  You can create them through the Sandbox Client Registration process [here](https://sandbox.demo.ndi.gov.sg/clnreg):

![Client Registration Flow](/assets/lib/trusted-access/appwebdev/img/clientregistrationflow.png)

