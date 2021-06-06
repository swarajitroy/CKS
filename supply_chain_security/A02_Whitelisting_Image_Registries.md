# Whilelisting Image Registry

This can be acheived via AdmissionControllers in Kubernetes. There are multiple ways to achieve this, 

1. Using out of the box Validating Admission Controller  - ImagePolicyWebhook and setup a custom webhook to decide for the ImagePolicyWebhook. 
2. Use custom Validating Admission Controller - configured with a custom webhook to decide for the ImagePolicyWebhook. 
3. Use custom Validating Admission Controller - configured with OPA server


## 01. ImagePolicyWebhook 
---

- Setup the Webhook - which knows how to parse requests coming from the Kubernetes API Server and reply is the proper format (yes/no) for decision making
- Enable ImagePolicyWebhook at the API Server 
- Test a scenario

### 01.A Setup the Webhook
---

1. We will use a docker image as described at https://github.com/kainlite/kube-image-bouncer - flavio/kube-image-bouncer
2. Create the Certificate and Private Key using OpenSSL (server side - which is the Kube Image Bouncer) 
3. Create a Secret of TLS type with the  Kube Image Bouncer server certificate and private key 
4. Design a Pod with image flavio/kube-image-bouncer, with a command argument to whitelist few registries and injecting the secret as volumes
5. Create a Deployment with the pod 
6. Expose the deployment as a NodePort Service

### 01.B Enable ImagePolicyWebhook at the API Server 
---

1. Create the Certificate and Private Key using OpenSSL (client side - which is the api server)   
2. Create configuration json file for ImagePolicyWebhook 
3. Enable ImagePolicyWebhook at API Server admission controller and attach the configuration json file for ImagePolicyWebhook 
4. Create the KubeConfig style API server config file for the weebhook 

### 01.C Test Scenario
---

1. Try to run an image from a repostory from Docker and not using LATEST tag - it should pass
2. Try to run an image from a repostory from Docker and using LATEST tag - it should fail
3. Try to run an image from a repostory from Quay and not using LATEST tag - it should fail

```

kind: Deployment
metadata:
  name: image-bouncer-webhook
spec:
  selector:
    matchLabels:
      app: image-bouncer-webhook
  template:
    metadata:
      labels:
        app: image-bouncer-webhook
    spec:
      containers:
        - name: image-bouncer-webhook
          imagePullPolicy: Always
          image: "kainlite/kube-image-bouncer:latest"
          args:
            - "--cert=/etc/admission-controller/tls/tls.crt"
            - "--key=/etc/admission-controller/tls/tls.key"
            - "--debug"
            - "--registry-whitelist=docker.io,k8s.gcr.io"
          volumeMounts:
            - name: tls
              mountPath: /etc/admission-controller/tls
      volumes:
        - name: tls
          secret:
            secretName: tls-image-bouncer-webhook
                                                      
```                                                      
                                                      
