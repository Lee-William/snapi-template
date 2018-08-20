# Getting Started 

## Create Client Credentials

You will need client credentials for your Web App to perform authentication and authorization with Sandbox ASP. You can create them through the Sandbox Client Registration process at [https://sandbox.ndi.io/clnreg](https://sandbox.ndi.io/clnreg):

![Create Client Credentials](/assets/lib/img/generate-client-credentials.png)


## Create Test Digital Identity

The NDI Sandbox provides the DemoKeys app which let you enrol test users to NDI Sandbox and create digital identities for them.  Download and install the DemoKeys app from https://sandbox.ndi.io and follow the steps to create test user ids which you can use to try out the ASP API.

![Create Test Digital Identity](/assets/lib/img/create-test-digital-identity.png)

## Make the First Call

After ensuring your test users are working, you are all set to make your first API call. Invoke the ASP authorization endpoint as follows:
`https://sandbox.ndi.sg/api/v1/asp/auth?client_id={client_id}&response_type=code&scope=openid&redirect_uri={redirect_uri}&
state={state}&nonce={nonce}`

| Parameters   | Description |
| ------------ | ----------- |
| client_id    | Client ID assigned to your Web/Mobile App by the Client Registration process |
| redirect_uri | URI to return the authorization code to your Web/Mobile App, e.g. `https://www.example.com/redirect` |
| state        | A one-time randomly generated unique string which will be returned along with the authorization code as a means to prevent CSRF and can also be used to maintain state |
| nonce        | A one-time randomly generated unique string which will be included in the ID token as a means to prevent replay attacks |

You may now proceed to explore the other ASP API at the Developer Portal and download the Demo-WebApp sample code as a reference for integrating the ASP API into your Web App/Mobile App. If you are building a mobile app, you may want to refer to Integration with the NDI App.

## Register Your Web / Mobile App

When you complete the integration of your App with NDI using the NDI Sandbox, you are ready to register your App at the Developer Portal to start the actual testing and deployment process. During the NDI Client App Registration process, you will be required to provide information about your organization, detail use case description of your App, and the expected TPS of authentication and other API calls.  This information will be taken as inputs to the review process to grant the use of NDI to your App.

Visit the Developer Portal for more information on NDI Client App Registration.
