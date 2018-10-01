### Exception Handling

#### General Error Handling
<br/>

> Your Web App is to handle error responses from the ASP properly to
> provide a smooth and pleasant user experience. For example, error
> messages returned by the ASP usually contains technical details which
> may confuse and alarm the user unnecessarily. Your Web App should not
> simply display these error messages verbatim as and when these errors
> occur, instead your Web App should interpret these errors and take
> appropriate remedial action where possible, and display user-friendly
> messages when user attention is required.
>
> The following are a non-exhaustive list of errors and the proper way
> of exception handling to implement.

#### Authentication Error
<br/>

> If you are using your own login UI to capture the NDI Id of the user,
> ensure your login UI allows the user to re-attempt the login up to 3 –
> 5 trials. Your login UI is to be protected by anti-bot mechanism such
> as captcha.

#### Client Error (HTTP 4xx)
<br/>

> Client errors are mostly caused by problems at the client side (i.e.
> your Web App), such as badly formed requests, missing mandatory
> fields, invalid URL, etc. There is no point retrying when your Web App
> encountered Client Errors, ensure your Web App handles user entry
> properly to eliminate bad request and missing fields issues.

#### Server Error (HTTP 500)
<br/>

> Server errors are mostly caused by problems at the server side (i.e.
> the ASP), which might be temporal in nature. Your Web App should retry
> the request a couple of times and should display a “Service
> Unavailable” message if the same error persisted.

#### Timeout Error
<br/>

> Timeout errors may be caused by network congestion or system
> encountering high load, which may be intermittent in nature. Your Web
> App should retry the request a couple of times but use the exponential
> backoff approach to avoid further contributing to the congestion.
> Display the “Service Unavailable” message if the timeout error
> persisted.

#### No Response
<br/>

> Your Web App may receive no response when there is a network or system
> outage. Your Web App should implement timeout to prevent your users
> from waiting indefinitely. Display the “Service Unavailable” message
> if the no response situation persisted

#### Error Codes
<br/>