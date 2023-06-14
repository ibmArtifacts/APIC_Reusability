# APIC_Reusability  
This document is to capture different ways to reuse code or apis on APIC.  
Some api code reuse are as follows, but not limited to:
- reusing developed API code policies (copying and pasting),
- reusing already developed APIs as micro services,
- custom policies (user-defined policies)

## Sample Service
The sample service is the API we'll be using for this document.
The following ([dynamic-basic-auth.yaml](https://github.com/ibmArtifacts/APIC_Reusability/blob/main/dynamic-basic-auth.yaml)) is a basic API which contains an APIC Gatewayscript policy and an Invoke policy for the purpose of using the consumer's username and password via basic authorization http header for the backend service to authenticate/validate the consumer, thus this api is called "dynamic-basic-auth" since the basic will not be static for the backend call.  
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/d059c082-bb7f-4497-a02a-66dc8959428c)  
We will be using this as a sample service for this document.  
NOTE: The Invoke policy contains a test url, which you should change to your own test url.  
The current Invoke policy uses a https://www.mockable.io/ servuce, which is a good public api mock service where you may create a quick loopback service for the dynamic-basic-auth api to invoke.

### What the sample service is doing
The sample api dynamic-basic-auth extracts the http header basic auth credentials and sets them to the Invoke Policy Username and Password fields, then the Invoke Policy will call the loopback service on mockable, where we may review whether the http basic auth is being passed properly.  
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/952c00b9-2d57-46b2-8b12-4850d01164dd)  
  
## Reusing developed API code policies (copying and pasting)  
One way to reuse policy flows is simply copying and pasting the snippet of code from the assembly -> execute section of the yaml of the api.
For example, from the sample api, you will find lines 16 to 29 make up the gatewayscript policy containing the logic of this gateway policy:  
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/889da17d-eaa1-4274-a7a7-71d0b10f904b)  
This code may be copied and placed in another APIC API assembly > execute section to be used in another api.  

## Reusing already developed APIs as micro services  
If an api already provides the result of a function you require, then you may call that api rather than copying the same logic over to the api you are working on.  
For example, if the sample api response is something required in another api, an Invoke Policy may be used to call the sample API with the following endpoint: ` https://127.0.0.1:9443/$(api.org.name)/$(api.catalog.name)/dynamic-basic-auth/  `
NOTE: This is assuming that both the apis will be published in the organization and catalog, which the `$(api.org.name)` and `$(api.catalog.name)` designates the current runtime environment where the API is being invoked from.  
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/fa7d99fe-2381-41f3-a78b-224c9228c8d0)  

## Custom Policies (user-defined policies)  
Another concept of reusability are to create a [User-Defined Policy](https://www.ibm.com/docs/en/api-connect/10.0.5.x_lts?topic=constructs-user-defined-policies) (or custom policy), which are packaged policies to handle certain general processing of the request or response.  
The [dynamic-basic-auth-udp.zip](https://github.com/ibmArtifacts/APIC_Reusability/blob/main/dynamic-basic-auth-udp.zip) contains the extracted policies from the sample API [dynamic-basic-auth.yaml](https://github.com/ibmArtifacts/APIC_Reusability/blob/main/dynamic-basic-auth.yaml). The code for the udp may be reviewed from the [dynamic-basic-auth-udp.yaml](https://github.com/ibmArtifacts/APIC_Reusability/blob/main/dynamic-basic-auth-udp.yaml).  
### How the dynamic-basic-auth-udp is created  
1. From the polcies section of the APIM, toggle the Source icon on the top right section of the policy section to get to the API yaml code.
2. Copy the desired policies from the API yaml code and past it into the UDP yaml file. The udp file is created using the IBM APIC Documentation [Defining, packaging, and publishing a catalog-scoped policy for the API Gateway](https://www.ibm.com/docs/en/api-connect/10.0.5.x_lts?topic=dppcspag-structure-catalog-scoped-user-defined-policy-yaml-file)  
3. Zip up the policy as shown in the diagram below and upload to the catalog.


![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/bb8e2001-b673-4e3c-b0fb-b42d5eeb70fe)  

Here is another sample UDP: https://github.com/ibmArtifacts/custom-jwt-invoke  


# Other reusable objects on APIC  
There are resources that may be created to be used throughout the APIs. Some of those are described in this section.  
## User Registries  
More details about User Registries may be found in the [IBM APIC Documentation: User registries overview](https://www.ibm.com/docs/en/api-connect/10.0.5.x_lts?topic=security-user-registries-overview).  
The user registries may be found in the Resources section of both the Cloud Manager (CM) and API Manager (APIM).  
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/f870a44e-cf69-4bf0-a2f3-da340719b599)  

User registries may be configured in the CM and/or APIM. The difference is:  
- CM based configurations are managed by CM authorized users like operational or system admin teams. The user registries visibility may be set to APIM.  
- APIM based configurations are managed by the provider API development teams.  

The user registry may be used in many places, but most cases will be for the following:  

- Local registries will be used for CM, APIM, and consumer organization logins.  
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/9e052327-8edb-4a0a-90e5-962764991077)  
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/08f616fe-a5ec-4c10-8529-253a870da2e5)  

- Authentication URL, LDAP and OIDC are used as a part of the security schema for either Basic Auth or OAuth 2.0.  
  - Authorization URL or LDAP is configured within the OAuth Object first:  
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/80bc1960-3dda-467d-981e-a6c173d47948)  
  - Authorization URL and/or LDAP may be used for each API per resource set at the top level of the APIM:
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/3ecd7422-ceaf-4293-8a0d-ba55b109c9b4)  

## TLS  
More details about TLS profiles may be found in the [IBM APIC Doumentation: TLS profiles overview](https://www.ibm.com/docs/en/api-connect/10.0.5.x_lts?topic=security-tls-profiles-overview).  

Like the User resgitries, the TLS profile may be configured in the CM or the APIM (not TLS Server profiles), similarly, the TLS profiles in the CM are managed by operation team or admin teams, where TLS Client Profiles in the APIM section are managed by the provider API development teams.  
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/780883ed-a56b-4c56-830b-dbefc47cab4a)  
  
  
## OAuth providers  
More details about OAuth providers may be found in the [IBM APIC Documentation: OAuth Provider overview](https://www.ibm.com/docs/en/api-connect/10.0.5.x_lts?topic=security-oauth-provider-overview).  

Like the User registries and TLS Client Profiles, the OAuth may be configured in the CM or the APIM.  
This is an object that is created in the resource to be used or reused per API.  

![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/49dd70bd-cee7-4db6-85da-f914ed662838)  


  





