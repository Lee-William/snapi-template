# Release History

| Date | Revision | By | Comments |
| ------ | -- | ----------- | ------------------- |
| 23-July-2018   | 0.1 | Yee Wee LOW | First Draft |

# Configure Sandbox

The NDI Sandbox is a full-featured system (albeit much diminished in scale and resiliency compared to the actual system) showcasing the key use cases and flows of NDI.  It serves as a playground for developers to experiment with the API to quickly gain an understanding of how NDI works.  Follow these steps to start your exploration.  

## Create Test Digital Identity

The NDI Sandbox provides the DemoKeys Enrolment application which let you enrol test users to NDI Sandbox and create digital identities for them.  Create your test users with the following steps:

1. Open the browser to https://sandbox.ndi.io

2. Click Create Test Digital Identity, the following screen appears:
 
3. Click Verify Identity on the DemoKeys Enrolment screen.  The following screen appears:
  
4. Enter your preferred user id and your email address.  Click Generate OTP for an OTP to be sent to your email address.

5. Enter the OTP and click Verify.  On successful verification, dismiss the screen to go back to the DemoKeys Enrolment screen.
   
6. At the DemoKeys Enrolment screen, enter the User Id and PIN and click Generate Digital Keys.
   
7.	On successful generation of keys, click Enable NDI Web Notification to allow NDI to send authentication request via Web Push to your desktop or mobile browser.

8.	Finally, click Activate to complete the user enrolment to NDI.

You may create up to 3 test users using the same email address.  Take note that the enrolment of each test user should be done on different desktops or mobile devices as the Web Push mechanism delivers authentication requests to device endpoints rather than to users.

## Try Out NDI Login

You should use the Demo-WebApp application provided by the Sandbox to ensure the test users you created are working.

1. Open browser to https://sandbox.ndi.io

2. Click Try Out NDI Login, the Demo-WebApp screen appears, click NDI Login to launch the NDI Login screen:
 
3. Enter the test user id and click Login.

4. You should receive a Web Push notification informing you of an authentication request. Clicking on the notification opens the DemoKeys Form Factor screen to let you enter the PIN to unlock the digital key of the test user*.

> *Note:
The Web Push used to send authentication notification to desktop/mobile browser and DemoKeys Form Factor screen is meant to emulate the actual form factor authentication flow in which the notification is sent to the user’s mobile device, where the user enters the PIN to unlock the form factor hosted on the mobile device.

## Make the First Call
After ensuring your test users are working, you are all set to make your first API call.  Invoke the HSS signHash endpoint as follows:

```
POST https://sandbox.ndi.sg/api/v1/hss/signatures/signHash
Request body (signRequest):
{
    user: {
        ndi_id: {signer_user_id}
    },
    client: {
        client_id: {your_client_id},
        redirect_uri: {your_redirect_uri},
        state: {your_app_state},
        expiry: {transaction_expiry_datetime},
        doc_name: {document_name},
        vcode: {verification_code}
    },
    clientData: {
        hash_alg: {hash_algorithm_used},
        hash: {data_to_be_signed}
    }
}
```

Replace {signer_user_id} with NDI id that you collected from the user, if this value is not provided, the user will be redirected to NDI Login page
{your_client_id} with your email address  
{your_redirect_uri} with client app registration URI,  to return the sign response to your Web App, e.g. https://www.signapp.com/sign/response
{your_app_state} with alphanumeric string to prevent CSRF or restore the previous of your application, e.g. 10af9431enf5
{transaction_expiry_datetime} with datetime given to process the request before timeout in yyyy-mm-ddThh:mm:ss+08:00 e.g. 2018-06-21T18:36:36+08:00
{document_name} with name of the document to be signed that will be displayed in the NDI App; this value shall be displayed on your application when the user initiates the signing flow
{verification_code} with the generated verification code for the ease of verification performed by the user; this value shall be displayed on your application when the user initiates the signing flow e.g. 168899
{hash_algorithm_used} with hash algorithm used to generate the document hash; this value shall be displayed on your application when the user initiates the signing flow
{data_to_be_signed} with the hash generated with the document

On successful invocation, the NDI Login screen will appear if the ndi_id is not provided. After acquiring the NDI id, HSS will forward the info for verification, and prepare the sign request to the user’s form factor.

Alternatively, you may explore and try out the other HSS API at the Developer Portal  and download the Demo-SignApp sample code as a reference for integrating the HSS API into your Web App.

## Register Your Signature Web App

When you complete the integration of your Web App with NDI using the NDI Sandbox, you are ready to register your Web App at the Developer Portal to start the actual testing and deployment process.  During the Client App Registration process, you will be required to provide information about your organization, detail use case description of your Web App, and the expected TPS of authentication and other API calls.  This information will be taken as inputs to the review process to grant the use of NDI to your Web App.
Visit the Developer Portal for more information on Client App Registration.
