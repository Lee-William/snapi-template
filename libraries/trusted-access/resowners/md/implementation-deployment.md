# Deployment

## Pre-Requisites

These are the pre-requisites for ASP deployment to the federated site:

| Components | Description |
| ------ | ----------- |
| Servers – to run the ASP and DS micro-services   | These may be commodity VMs running a Linux based OS (RedHat Enterprise Linux recommended).  The ASP and DS micro-services support load-balancing and horizontal scaling based on virtual IP address. <br><br> A container version of the ASP Core and DS Replica is available to run on PaaS / container platform which supports Docker. 
| Database – to host the DS data stores | These may be any SQL database (MySQL Enterprise recommended). <br><br> If your existing database is NoSQL, the DS Data Services micro-service will have to be customized to integrate with your NoSQL, as there is currently no industry standards for accessing NoSQL databases.
| Hardware Secure Module (HSM) – to store ASP keys    | These may be any FIPS L3 certificate HSM which supports PKCS#11. If your HSM does not support PKCS#11, the ASP Crypto Services micro-service will have to be customized to integrate with your HSM using proprietary interfaces. 
Distributed Cache – to store transient data shared by the ASP micro-service instances | These may be any distributed cache which supports the JCache interface (JSR107). <br><br> If your distributed cache does not support JCache, the ASP Data Services micro-service will have to be customized to integrate with your distributed cache system.  Alternatively, you may use the distributed cache solution of the ASP Core package.

## Directory Services Replica Deployment
The DS deployment steps consist of the following scripted deployment packages:

### DS Subscriber API
1. Run the following script to install the DS Subscriber API to your Web Server / API Gateway server:
>```
> sudo install_dssub.sh
> ```

2. The DS Subscriber API listens to port 443 by default, you may change it by amending the port attribute in the DS Subscriber config file (default.json).

3. To expose the DS Subscriber API through your API Gateway, you may import the following swaggerdoc (OAS3) dssub.yaml to your API Gateway.

4. Configure firewalls and other DMZ devices (e.g. reverse-proxy) to allow the Directory Services (Master) endpoint access to the DS Subscriber API via Internet.  The Directory Services (Master) endpoint and TLS certificate info will be provided separately.

### DS Data Stores
1. Run the following script to create the database schemas of the DS data stores to the database server designated for the data stores.

>```
> sudo install_dsdata.sh
> ```

### DS Micro-Services

1. Run the following script to install the DS Subscriber API to the server designated to run the DS micro-services:

>```
> sudo install_dssvc.sh
> ```

2. The DS micro-dervices listens to port 443 by default, you may change it by amending the port attribute in the DS micro-services config file (default.json).

3. Update the database connection string, user id and password to the DS Micro-Services config file (default.json) under the database section.

4. To expose the DS micro-services API through your API Gateway, you may import the following swaggerdoc (OAS3) dssvc.yaml to your API Gateway.

5. Configure firewalls and the necessary routing rules to allow the DS Subscriber API access to the DS micro-services endpoints.

6. Configure firewalls and the necessary routing rules to allow the DS micro-services access to the database server hosting the DS data stores.

## ASP Core Deployment