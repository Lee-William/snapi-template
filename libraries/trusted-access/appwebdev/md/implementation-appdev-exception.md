### Exception Handling
<br/>

#### General Error Handling
<br/>

Your Mobile App is to handle error responses from the NDI App and the ASP properly to provide a smooth and pleasant user experience.  For example, error messages returned by the ASP usually contains technical details which may confuse and alarm the user unnecessarily.  Your Mobile App should not simply display these error messages verbatim as and when errors occur, instead your Mobile App should interpret these errors and take appropriate remedial action where possible, and display user-friendly messages when user attention is required.
The following are a non-exhaustive list of errors and the proper way of exception handling to implement.

#### Authentication Error
<br/>

Your Mobile App should check for error when the NDI App calls your callback method.  If there is an error, your Mobile App should allow the user to re-attempt login for up to 3 – 5 times.

#### Client Error (HTTP 4xx)
<br/>

Client errors are mostly caused by problems at the client side (i.e. your Mobile App), such as badly formed requests, missing mandatory fields, invalid URL, etc.  There is no point retrying when your Mobile App encountered Client Errors, ensure your Mobile App handles user entry properly to eliminate bad request and missing fields issues.

#### Server Error (HTTP 500)
<br/>

Server errors are mostly caused by problems at the server side (i.e. the ASP), which might be temporal in nature.  Your Mobile App should retry the request a couple of times and should display a “Service Unavailable” message if the same error persisted.

#### Timeout Error
<br/>

Timeout errors may be caused by network congestion or system encountering high load, which may be temporal in nature.  Your Mobile App should retry the request a couple of times but use the exponential backoff approach to avoid further contributing to the congestion.   Display the “Service Unavailable” message if the timeout error persisted.

#### No Response
<br/>

Your Mobile App may receive no response when there is a network or system outage.  Your Mobile App should implement timeout to prevent your users from waiting indefinitely.  Display the “Service Unavailable” message if the no response situation persisted.

#### Error Codes
<br/>
