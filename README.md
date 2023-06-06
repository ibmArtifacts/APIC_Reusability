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
https://www.mockable.io/ is a good public api mock service, which you may create a quick loopback service for the dynamic-basic-auth api to invoke.
  
## Reusing developed API code policies (copying and pasting)  
One way to reuse policy flows is simply copying and pasting the snippet of code from the assembly > execute section of the yaml of the api.
For example, from the sample api, you will find lines 16 to 29 make up the gatewayscript policy containing the logic of this gateway policy:  
![image](https://github.com/ibmArtifacts/APIC_Reusability/assets/66093865/6748ef31-90ac-411d-8199-8f4f91e973e3)  

