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

We will use a docker image as described at https://github.com/kainlite/kube-image-bouncer - flavio/kube-image-bouncer. Create the Certificate and Private Key using OpenSSL (server side - which is the Kube Image Bouncer) 

```
ubuntu@ip-172-31-22-219:~/imagepolicywebhook/server_cert$ openssl req  -nodes -new -x509 -keyout webhook-key.pem -out webhook.pem
Can't load /home/ubuntu/.rnd into RNG
140258938556864:error:2406F079:random number generator:RAND_load_file:Cannot open file:../crypto/rand/randfile.c:88:Filename=/home/ubuntu/.rnd
Generating a RSA private key
........+++++
....+++++
writing new private key to 'webhook-key.pem'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:
State or Province Name (full name) [Some-State]:
Locality Name (eg, city) []:
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:bouncer_webhook
Email Address []:

```

Create a Secret of TLS type with the  Kube Image Bouncer server certificate and private key 

```

ubuntu@ip-172-31-22-219:~/imagepolicywebhook/server_cert$ kubectl create secret tls tls-image-bouncer-webhook --cert=webhook.pem --key=webhook-key.pem
secret/tls-image-bouncer-webhook created
ubuntu@ip-172-31-22-219:~/imagepolicywebhook/server_cert$ kubectl describe secret tls-image-bouncer-webhook
Name:         tls-image-bouncer-webhook
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  kubernetes.io/tls

Data
====
tls.crt:  1318 bytes
tls.key:  1708 bytes


```

Design a Pod with image flavio/kube-image-bouncer, with a command argument to whitelist few registries and injecting the secret as volumes, Create a Deployment with the pod 
Expose the deployment as a NodePort Service.

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

                                      
                                                      
