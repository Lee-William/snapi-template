### Failover Considerations
<br/>

This section describes the recovery action which may be taken in the event of the following outages.

#### OCSP

The ASP calls the OCSP service to check certificate status during user authentication. In the event the OCSP service is not available, the federated site may activate the following fallback mechanisms:

##### Verification by CRL

The ASP may be configured to switch to verifying certificate status using the local copy of the CRL. The default trigger for the switch is based on the number of timeout errors encountered calling the OCSP service, the trigger value may be set in the ASP configuration. Alternatively, the switch may be triggered by an external event sent by the federated site’s monitoring system.

As the CRL may not contain the latest certificate revocation status, the federated site has to put in place risk assessment to determine the type of transactions to allow during this fallback state.

##### Verification by Form Factor Status

The form factor record fetched from the Directory Services contains the form factor status which closely reflects the status of its associated certificate. In most situations, the revocation of certificates is done through the RA API, which will update the form factor status of the associated form factor record in the Directory Services (master) accordingly to “revoked”. The updated form factor status is then propagated to the Directory Services replicas at federated sites.

The ASP checks the form factor status to ensure it is “active” before proceeding with user authentication. For an organization which uses the Deep Integration federation option, the organization’s authentication system will have to perform the form factor status
check.

As there is a small latency in propagation of updates to the Directory Services replicas, the form factor status may not reflect the latest certificate revocation status of its associated certificate. The federated site has to put in place risk management mechanism to determine the type of transactions to allow during this fallback
state.

#### Form Factor Authenticator

This scenario applies to remotely hosted Form Factor Authenticator services. In the event of the remotely hosted Form Factor Authenticator service is not available, the ASP will switch to use the alternate endpoint URI to invoke the alternate instance of the Authenticator service. The default trigger for the switch is based on the number of timeout errors encountered calling the Authenticator service, the trigger value may be set in the ASP configuration. Alternatively, the switch may be triggered by an external event sent by the federated site’s monitoring system.

For an organization which uses the Deep Integration federation option, the organization’s authentication system will have to switch to use the alternate endpoint URI to invoke the alternate instance of the
Form Factor Authenticator service.

#### Directory Services
<br/>

The Directory Services are typically deployed on-site at the federated site in the form of the Directory Service Replica. If the on-site Directory Service Replica failed, it is usually due to factors within the federated site, the resiliency measure and recovery action should be part of the operation practices of the federated site.

The Directory Services Replica has a cache mechanism (DS cache) which can be enabled to cache a configurable amount of frequently accessed form factor records into a distributed in-memory data cache. In the event of a database-related outage, the DS cache will continue to function and let the ASP fetch cached form factor records.

##### Directory Services Replication Failure
<br/>

In the event of the Directory Service replication service failure, updates will not be propagated to the Directory Services Replicas at federated sites. New users onboarded to NDI during the outage will not be able to login using NDI at federated sites. The federated site may continue to rely on its Directory Services replica for existing NDI users as long as the OCSP service is available to provide the latest certificate revocation status.
