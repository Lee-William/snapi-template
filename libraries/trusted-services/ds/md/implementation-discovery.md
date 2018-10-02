### Trusted List (aka Discovery Document)
<br/>

To verify the sign response, there will be requests for resources such as the security tokens and the public keys.  To simplify the implementation and discovery of these endpoints and resources, you can use a discovery document – in JSON format, and downloadable from a well-known location – which describes the configuration and supported features.

You will be able to download the HSS discovery document from the following location: 

````
GET {basePath}/hss/discovery
````

Your Web App should download the HSS discovery document periodically (it is safe to download the document on a daily basis) to keep up to date of the HSS’s configuration, as the HSS endpoints may change from time to time (e.g. from api/v1 to api/v2), and the public keys are also refreshed regularly.
