# Whilelisting Image Registry

This can be acheived via AdmissionControllers in Kubernetes. There are multiple ways to achieve this, 

1. Using out of the box Validating Admission Controller  - ImagePolicyWebhook and setup a custom webhook to decide for the ImagePolicyWebhook. 
2. Use custom Validating Admission Controller - configured with a custom webhook to decide for the ImagePolicyWebhook. 
3. Use custom Validating Admission Controller - configured with OPA server
