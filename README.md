# APIC_Reusability  
This document is to capture different ways to reuse code or apis on APIC.  
Some api code reuse are as follows, but not limited to:
- reusing developed API code policies (copying and pasting),
- reusing already developed APIs as micro services,
- custom policies (user-defined policies)

## Sample Service
The following ([dynamic-basic-auth.yaml](https://github.com/ibmArtifacts/APIC_Reusability/blob/main/dynamic-basic-auth.yaml)) is a basic API which contains an APIC Gatewayscript policy and an Invoke policy for the purpose of using the consumer's username and password via basic authorization http header for the backend service to authenticate/validate the consumer, thus this api is called "dynamic-basic-auth" since the basic will not be static for the backend call.  
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/50fd8623-0588-4bc7-a272-6194a6f48ebf)  
We will be using this as a sample service for this document.  
NOTE: The Invoke policy contains a test url, which you should change to your own test url.  
The current Invoke policy uses a https://www.mockable.io/ servuce, which is a good public api mock service where you may create a quick loopback service for the dynamic-basic-auth api to invoke.

### What the sample service is doing
The sample api dynamic-basic-auth extracts the http header basic auth credentials and sets them to the Invoke Policy Username and Password fields, then the Invoke Policy will call the loopback service on mockable, where we may review whether the http basic auth is being passed properly.  
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/73d3eede-4fca-456d-8a8e-ae7bb3886a0b)  
  
## Reusing developed API code policies (copying and pasting)  
One way to reuse policy flows is simply copying and pasting the snippet of code from the assembly > execute section of the yaml of the api.
For example, from the sample api, you will find lines 16 to 29 make up the gatewayscript policy containing the logic of this gateway policy:  
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/6748ef31-90ac-411d-8199-8f4f91e973e3)  
This code may be copied and placed in another APIC API assembly > execute section to be used in another api.  

## Reusing already developed APIs as micro services  
If an api already provides the result of a function you require, then you may call that api rather than copying the same logic over to the api you are working on.  
For example, if the sample api response is something required in another api, an Invoke Policy may be used to call the sample API with the following endpoint: ` https://127.0.0.1:9443/$(api.org.name)/$(api.catalog.name)/dynamic-basic-auth/  `
NOTE: This is assuming that both the apis will be published in the organization and catalog, which the `$(api.org.name)` and `$(api.catalog.name)` designates the current runtime environment where the API is being invoked from.  ![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/fdd5cbc0-8325-4b7c-b08f-2fd648f386dc)  

## Custom Policies (user-defined policies)  
https://github.com/ibmArtifacts/custom-jwt-invoke  
