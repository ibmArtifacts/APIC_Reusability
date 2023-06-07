# APIC_Reusability  
This document is to capture different ways to reuse code or apis on APIC.  
Some api code reuse are as follows, but not limited to:
- reusing developed API code policies (copying and pasting),
- reusing already developed APIs as micro services,
- custom policies (user-defined policies)

## Sample Service
The sample service is the API we'll be using for this document.
The following ([dynamic-basic-auth.yaml](https://github.com/ibmArtifacts/APIC_Reusability/blob/main/dynamic-basic-auth.yaml)) is a basic API which contains an APIC Gatewayscript policy and an Invoke policy for the purpose of using the consumer's username and password via basic authorization http header for the backend service to authenticate/validate the consumer, thus this api is called "dynamic-basic-auth" since the basic will not be static for the backend call.  
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/c13a7d96-efa2-48e5-9b1f-d30872cee67e)  
We will be using this as a sample service for this document.  
NOTE: The Invoke policy contains a test url, which you should change to your own test url.  
The current Invoke policy uses a https://www.mockable.io/ servuce, which is a good public api mock service where you may create a quick loopback service for the dynamic-basic-auth api to invoke.

### What the sample service is doing
The sample api dynamic-basic-auth extracts the http header basic auth credentials and sets them to the Invoke Policy Username and Password fields, then the Invoke Policy will call the loopback service on mockable, where we may review whether the http basic auth is being passed properly.  
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/73d3eede-4fca-456d-8a8e-ae7bb3886a0b)  
  
## Reusing developed API code policies (copying and pasting)  
One way to reuse policy flows is simply copying and pasting the snippet of code from the assembly -> execute section of the yaml of the api.
For example, from the sample api, you will find lines 16 to 29 make up the gatewayscript policy containing the logic of this gateway policy:  
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/6748ef31-90ac-411d-8199-8f4f91e973e3)  
This code may be copied and placed in another APIC API assembly > execute section to be used in another api.  

## Reusing already developed APIs as micro services  
If an api already provides the result of a function you require, then you may call that api rather than copying the same logic over to the api you are working on.  
For example, if the sample api response is something required in another api, an Invoke Policy may be used to call the sample API with the following endpoint: ` https://127.0.0.1:9443/$(api.org.name)/$(api.catalog.name)/dynamic-basic-auth/  `
NOTE: This is assuming that both the apis will be published in the organization and catalog, which the `$(api.org.name)` and `$(api.catalog.name)` designates the current runtime environment where the API is being invoked from.  
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/fdd5cbc0-8325-4b7c-b08f-2fd648f386dc)  

## Custom Policies (user-defined policies)  
https://github.com/ibmArtifacts/custom-jwt-invoke  

# Other reusable objects on APIC  
There are resources that may be created to be used throughout the APIs. Some of those are described in this section.  
## User Registries  
More details about User Registries may be found in the [IBM APIC Documentation: User registries overview](https://www.ibm.com/docs/en/api-connect/10.0.5.x_lts?topic=security-user-registries-overview).  
The user registries may be found in the Resources section of both the Cloud Manager (CM) and API Manager (APIM).  
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/4fc51359-1100-468c-8eed-9e49ccff4aeb)  

User registries may be configured in the CM and/or APIM. The difference is:  
- CM based configurations are managed by CM authorized users like operational or system admin teams. The user registries visibility may be set to APIM.  
- APIM based configurations are managed by the provider API development teams.  

The user registry may be used in many places, but most cases will be for the following:  

- Local registries will be used for CM, APIM, and consumer organization logins.  
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/1ca40621-9fd4-4e20-ad0a-612de9fd7f0d)  
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/e8d193cf-dd1e-47d2-a838-bf95879fb44e)  

- Authentication URL, LDAP and OIDC are used as a part of the security schema for either Basic Auth or OAuth 2.0.  
  - Authorization URL or LDAP is configured within the OAuth Object first:  
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/595fcf2f-af1b-4c02-a4f8-8501bbf48320)  
  - Authorization URL and/or LDAP may be used for each API per resource set at the top level of the APIM:
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/14dab9e9-076b-4f52-9552-3aa8a09873e4)  

## TLS  
More details about TLS profiles may be found in the [IBM APIC Doumentation: TLS profiles overview](https://www.ibm.com/docs/en/api-connect/10.0.5.x_lts?topic=security-tls-profiles-overview).  

Like the User resgitries, the TLS profile may be configured in the CM or the APIM (not TLS Server profiles), similarly, the TLS profiles in the CM are managed by operation team or admin teams, where TLS Client Profiles in the APIM section are managed by the provider API development teams.  
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/31b715a1-f976-4786-8be9-516f244a26cb)  

## OAuth providers  
More details about OAuth providers may be found in the [IBM APIC Documentation: OAuth Provider overview](https://www.ibm.com/docs/en/api-connect/10.0.5.x_lts?topic=security-oauth-provider-overview).  

Like the User registries and TLS Client Profiles, the OAuth may be configured in the CM or the APIM.  
This is an object that is created in the resource to be used or reused per API.  

![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/3312f5a0-e36d-4399-a12f-fea837589071)  


  





