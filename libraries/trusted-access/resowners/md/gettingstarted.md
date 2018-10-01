### Step by Step Guide

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
   ![Enrolment Flow](/assets/lib/trusted-access/resowners/img/enrolmentflow.png)

##### Try Out NDI Login
<br/>

You should use the Demo-WebApp application provided by the Sandbox to ensure the test users you created are working.

1. Open browser on your mobile device to https://sandbox.demo.ndi.gov.sg/demowapp/home

2. Click Try Out NDI Login, the Demo-WebApp screen appears, click NDI Login to launch the NDI Login screen:
![NDI Login Screen](/assets/lib/trusted-access/resowners/img/ndi-web-login.png)

3. Enter the test user id and click Login.
4. You should receive a push notification on your device informing you of an authentication request. Clicking on the notification opens the DemoKeys Form Factor screen to let you enter the PIN to unlock the digital key of the test user.

##### Create Client Credentials for your applications
<br/>

You will need client credentials for your Web/Mobile App to perform authentication and authorization with Sandbox API.  You can create them through the Sandbox Client Registration process [here](https://sandbox.demo.ndi.gov.sg/clnreg):

![Client Registration Flow](/assets/lib/trusted-access/resowners/img/create-client-credentials.png)

<br/>

##### Make the First Call
<br/>

After ensuring your test users are working, you are all set to make your first API call.  Invoke the ASP authorization endpoint as follows:

````
https://sandbox.api.ndi.gov.sg/asp/api/v1/asp/auth?client_id={your_client_id}&
                response_type=code&
scope=openid&redirect_uri={your_redirect_uri}&
state={random_state}&nonce={random_nonce}

````

Replace:	
+ {your_client_id} with the client id assigned to your Web App by the Client Registration process

+ {your_redirect_uri} with the URI to return the authorization code to your Web App, e.g. https://www.ganymede.com/code

+ {random_state} with a random alphanumeric string, e.g. 10af9431enf5

+ {random_nonce} with a random alphanumeric string, e.g. da5492bc0h

On successful invocation, the NDI Login screen appears.
You may now proceed to explore the other ASP API at the Developer Portal and download the Demo-WebApp sample code as a reference for integrating the ASP API into your Web App. 

##### Register Your Web App
<br/>

When you complete the integration of your Web App with NDI using the NDI Sandbox, you are ready to register your Web App at the Developer Portal to start the actual testing and deployment process.  During the NDI Client App Registration process, you will be required to provide information about your organization, detail use case description of your Web App, and the expected TPS of authentication and other API calls.  This information will be taken as inputs to the review process to grant the use of NDI to your Web App.

Visit the Developer Portal for more information on NDI Client App Registration.
