# Getting Started

# NDI Sandbox

## What NDI sandbox is about

The sandbox playground provides a safe environment to experiment NDI APIs and help on steps required to integrate with your application. The operations that you perform in the sandbox doesn't affect the live, public data on main NDI site. This guide directs you to create an application account, that you will use to register an application using NDI developer Portal Home Page following steps to call desired API’s.

Specifically, it tells you HOW TO

1. Register your Application
2. Get client ID and client secret key for your Application
3. Request Auth Code from API Gateway
4. Request Access token from API Gateway
5. Call the APIs

## How to register your application


## How to get the client ID and client secret key for your application


## How to get AuthCode from NDI API gateway


## How to request Access Token from NDI API gateway


## How to call NDI APIs

## Create Client Credentials

You will need client credentials for your Web App to perform authentication and authorization with Sandbox ASP. You can create them through the Sandbox Client Registration process at [link](https://sandbox.api.ndi.gov.sg/clnreg):

![Create Client Credentials]
(/assets/lib/img/generate-client-credentials.png)

## Create Test Digital Identity

The NDI Sandbox provides the DemoKeys app which let you enrol test users to NDI Sandbox and create digital identities for them.  Download and install the DemoKeys app from [link](https://sandbox.api.ndi.gov.sg) and follow the steps to create test user ids which you can use to try out the ASP API.

![Flow for creating test identity accounts](\assets\lib\appwebdev\img\create-test-digital-identity.png)

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

Visit this [link - TBD](https://sandbox.ndi.gov.sg) for more information on NDI Client App Registration.
