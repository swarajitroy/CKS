# Whilelisting Image Registry

This can be acheived via AdmissionControllers in Kubernetes. There are multiple ways to achieve this, 

1. Using out of the box Validating Admission Controller  - ImagePolicyWebhook and setup a custom webhook to decide for the ImagePolicyWebhook. 
2. Use custom Validating Admission Controller - configured with a custom webhook to decide for the ImagePolicyWebhook. 
3. Use custom Validating Admission Controller - configured with OPA server

For our work - we will do 1 and 3. 

## 01. ImagePolicyWebhook 
---

- Setup the Webhook - which knows how to parse requests coming from the Kubernetes API Server and reply is the proper format (yes/no) for decision making
- Enable ImagePolicyWebhook at the API Server 
- Test a scenario

### 01.A Setup the Webhook
---

1. We will use a docker image as described at https://github.com/kainlite/kube-image-bouncer - flavio/kube-image-bouncer
2. Create the Certificate and Private Key using OpenSSL (server side - which is the Kube Image Bouncer)
3. 
