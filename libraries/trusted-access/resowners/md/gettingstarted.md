### Getting Started
<br/>

#### NDI Sandbox
<br/>

The NDI Sandbox serves as a playground for developers to experiment with the API to quickly gain an understanding of how NDI works.  Follow these steps to start your exploration with NDI Sandbox.  

##### Create Test Accounts for NDI users
<br/>

The NDI Sandbox provides the NDI Demo application to allow you to enrol test users to NDI Sandbox and create digital identities for them.  Create your test users with the following steps:

1. Download and install the Sandbox Mobile App from:
   + Android users click [here](https://goo.gl/cb4VBA)
   + IOS Users click [here](https://goo.gl/JQo2iu)

2. Follow steps listed below to start registration process:
   ![Enrolment Flow](/assets/lib/trusted-services/ds/img/enrolmentflow.png)

##### Try Out NDI Login
<br/>

To test out the test accounts are created successfully,

1. Use your browser and navigate to our [login application](https://sandbox.demo.ndi.gov.sg/loginapp)

2. Enter the test user you have just created

3. Click on the "login" button and look out for the push notificaation on your NDI demo app informing you of an authentication request.

4. Clicking on the notification will open the NDI demo app Form Factor screen to let you enter the PIN to unlock the digital key of the test user*.

> *Note:
The Push Notification used to send authentication notification to desktop/mobile browser and DemoKeys Form Factor screen is meant to emulate the actual form factor authentication flow in which the notification is sent to the user’s mobile device, where the user enters the PIN to unlock the form factor hosted on the mobile device.

#### Create Client Credentials for your applications
<br/>

You will need client credentials for your Web/Mobile App to perform authentication and authorization with Sandbox API.  You can create them through the Sandbox Client Registration process [here](https://sandbox.demo.ndi.gov.sg/clnreg):

##### Make the First Call
<br/>

After ensuring your test users are working, you are all set to make your first API call.  Invoke the NDI Hash Signing endpoint as follows:

```
POST https://sandbox.api.ndi.sg/hss/api/v1/hss/signatures/signHash
```

```
Request body (signRequest):
{
    client_id: {your_client_id}, // must be populate by server
    client_secret: {your_client_secret}, // must be populate by server
    nonce: {this_txn_nonce},  // must be populate by server
    hash_alg: {hash_algorithm_used}, // may be hashed by frontend
    hash: {data_to_be_signed},  // may be displayed to the user
    login_hint:{signer_user_id},  // obtained from ndi_id user
    redirect_uri: {your_redirect_uri},
    scope: "signHash",
    response_type:"json", // default is "json"; alt. pkcs7  
    tx_id: {txn_id},
    tx_expiry: {txn_expiry_datetime},
    tx_doc_name: {document_name},
    tx_vcode: {verification_code}  // must be displayed to the user
}
```

Replace:
+ {your_client_id} with your client app registration id 
+ {your_client_secret} with client secret obtained during client registration 
+ {this_txn_nonce} with alphanumeric string to prevent CSRF or restore the previous of your application, e.g. 10af9431enf5
+ {hash_algorithm_used} with hash algorithm used to generate the document hash; this value shall be displayed on your application when the user initiates the signing flow{data_to_be_signed} with the hash generated with the document
+ {signer_user_id} with NDI id that you collected from the user, if this value is not provided, the user will be redirected to NDI Login page
+ {your_redirect_uri} with client app registration URI,  to return the sign response to your Web App, e.g. https://www.signapp.com/sign/response
+ {txn_id} with your unique identifier for this transaction
+ {txn_expiry_datetime} with datetime given to process the request before timeout in yyyy-mm-ddThh:mm:ss+08:00 e.g. 2018-06-21T18:36:36+08:00
+ {document_name} with name of the document to be signed that will be displayed in the NDI App; this value shall be displayed on your application when the user initiates the signing flow
+ {verification_code} with the generated verification code for the ease of verification performed by the user; this value shall be displayed on your application when the user initiates the signing flow e.g. 168899

On successful invocation, the NDI Login screen will appear if the ndi_id is not provided. After acquiring the NDI id, HSS will forward the info for verification, and prepare the sign request to the user’s form factor.

Alternatively, you may explore and try out the other HSS API at the Developer Portal  and download the Demo-SignApp sample code as a reference for integrating the HSS API into your Web App.

#### Explore NDI Sandbox 

Now you are ready to check out our API specs! 

> Helpful Resource: 
> Check out our tutorials [here](https://ndi-sandbox.gitbook.io/ndi-stack2018)

### Register Your Signature Web App

When you complete the integration of your Web App with NDI using the NDI Sandbox, you are ready to register your Web App at the Developer Portal to start the actual testing and deployment process. During the Client App Registration process, you will be required to provide information about your organization, detail use case description of your Web App, and the expected TPS of authentication and other API calls. This information will be taken as inputs to the review process to grant the use of NDI to your Web App.

We are currently building the NDI systems. Subscribe to our mailing list to look out for more details!
