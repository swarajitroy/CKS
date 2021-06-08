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
#Create Private Key 
ubuntu@ip-172-31-22-219:~/imagepolicywebhook/server_cert$ openssl genrsa -out webhook-key.pem 2048

#Create CSR from that private key with CN=image-bouncer-webhook.default.svc
ubuntu@ip-172-31-22-219:~/imagepolicywebhook/server_cert$ openssl req -new -key webhook-key.pem -out webhook-csr.pem

#Self sign CSR with Subject alternative names
ubuntu@ip-172-31-22-219:~/imagepolicywebhook/server_cert$ cat fd.ext
subjectAltName = DNS:image-bouncer-webhook, DNS:image-bouncer-webhook.default.svc, IP:172.31.22.219

ubuntu@ip-172-31-22-219:~/imagepolicywebhook/server_cert$ openssl x509 -req -days 365 -in csr.pem -signkey webhook-key.pem -out webhook.pem -extfile fd.ext

# View the certificate webhook.pem

ubuntu@ip-172-31-22-219:~/imagepolicywebhook/server_cert$ openssl x509 -text -in webhook.pem -noout
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            77:fd:a6:96:77:bd:3f:c4:78:bb:84:32:20:4b:e6:82:c1:67:c8:64
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: C = AU, ST = Some-State, O = Internet Widgits Pty Ltd, CN = image-bouncer-webhook.default.svc
        Validity
            Not Before: Jun  8 12:50:35 2021 GMT
            Not After : Jun  8 12:50:35 2022 GMT
        Subject: C = AU, ST = Some-State, O = Internet Widgits Pty Ltd, CN = image-bouncer-webhook.default.svc
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                RSA Public-Key: (2048 bit)
                Modulus:
                    00:c5:fa:18:06:8f:ca:c2:8e:b9:42:b4:12:02:cb:
                    a1:c3:60:a5:64:e3:9c:0b:6b:79:63:39:8e:c9:5b:
                    --
                    20:3f:92:6e:d9:c4:af:fd:b3:5b:3d:2c:4c:0f:2b:
                    e7:fb:46:be:97:83:e6:22:78:61:b8:5d:74:b0:f2:
                    cc:f1:cf:fc:47:33:f4:bc:3c:10:0c:59:6d:a0:ba:
                    11:9a:02:72:2e:6c:b2:2d:70:88:d5:85:44:f5:75:
                    12:ab
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Subject Alternative Name:
                DNS:image-bouncer-webhook, DNS:image-bouncer-webhook.default.svc, IP Address:172.31.22.219
    Signature Algorithm: sha256WithRSAEncryption
         8f:2d:06:5a:9f:36:3a:d0:55:15:0a:37:46:2b:f2:3f:3a:9a:
         d2:f1:6e:a8:4c:b3:ce:73:0b:31:af:c7:e0:fd:3c:6c:78:8b:
         --
         ea:57:98:a6:e0:04:d0:88:d9:d3:13:a2:8d:9b:d6:d6:85:a3:
         f9:4a:e7:7e:31:16:db:49:3e:bf:2c:7a:ab:d1:c4:b4:4f:7d:
         cb:86:b5:eb

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
   
ubuntu@ip-172-31-22-219:~/imagepolicywebhook/manifests$ cat deployment.yaml
apiVersion: apps/v1
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
          image: docker.io/kainlite/kube-image-bouncer:latest
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
    
    
ubuntu@ip-172-31-22-219:~/imagepolicywebhook/manifests$ kubectl create -f deployment.yaml
deployment.apps/image-bouncer-webhook created

ubuntu@ip-172-31-22-219:~/imagepolicywebhook/manifests$ kubectl get deployments
NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
application-a           1/1     1            1           4d15h
image-bouncer-webhook   1/1     1            1           106s

ubuntu@ip-172-31-22-219:~/imagepolicywebhook/manifests$ kubectl get pods
NAME                                     READY   STATUS    RESTARTS   AGE
application-a-6555d4f559-ck9pm           1/1     Running   0          4d15h
image-bouncer-webhook-65c8599b4c-fkqtr   1/1     Running   0          5s
jupiter                                  1/1     Running   0          9h
pluto                                    1/1     Running   0          9h
testpod                                  1/1     Running   0          5d11h

ubuntu@ip-172-31-22-219:~/imagepolicywebhook/manifests$ cat service.yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: image-bouncer-webhook
  name: image-bouncer-webhook
spec:
  type: NodePort
  ports:
    - name: https
      port: 443
      targetPort: 1323
      protocol: "TCP"
      nodePort: 30080
  selector:
    app: image-bouncer-webhook

ubuntu@ip-172-31-22-219:~/imagepolicywebhook/manifests$ kubectl create -f service.yaml
service/image-bouncer-webhook created

ubuntu@ip-172-31-22-219:~/imagepolicywebhook/manifests$ kubectl get svc
NAME                    TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)         AGE
image-bouncer-webhook   NodePort    10.105.127.49    <none>        443:30080/TCP   25s



```                

### 01.B Enable ImagePolicyWebhook at the API Server 
---

Create the Certificate and Private Key using OpenSSL (client side - which is the api server)   

```
ubuntu@ip-172-31-22-219:~/imagepolicywebhook/client_cert$ openssl req  -nodes -new -x509 -keyout api-server-client-key.pem -out api-server-client.pem
Can't load /home/ubuntu/.rnd into RNG
140006524670400:error:2406F079:random number generator:RAND_load_file:Cannot open file:../crypto/rand/randfile.c:88:Filename=/home/ubuntu/.rnd
Generating a RSA private key
.............................................................................................+++++
.......................................................+++++
writing new private key to 'api-server-client-key.pem'
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
Common Name (e.g. server FQDN or YOUR name) []:api-server
Email Address []:

```
Create the ImagePolicyWebhook configuration file - its in KubeConfig style. This file contacts the Webhook at  https://image-bouncer-webhook.default.svc:30080/image_policy - which runs as a node port service on port 30080. The image-bouncer-webhook.default.svc is mapped in /etc/hosts file with master node internal IP address.

```
ubuntu@ip-172-31-22-219:/etc/kubernetes/admission$ cat imagepolicyconfig.yaml
# clusters refers to the remote service.
clusters:
- name: image-checker
  cluster:
    certificate-authority: /etc/kubernetes/admission/webhook.pem    # CA for verifying the remote service.
    server: https://image-bouncer-webhook.default.svc:30080/image_policy  # URL of remote service to query. Must use 'https'.

contexts:
- context:
    cluster: image-checker
    user: api-server
  name: image-checker
current-context: image-checker
preferences: {}

# users refers to the API server's webhook configuration.
users:
- name: api-server
  user:
    client-certificate: /etc/kubernetes/admission/api-server-client.pem    #cert for the webhook admission controller to use
    client-key: /etc/kubernetes/admission/api-server-client-key.pem          # key matching the cert


```

Create configuration json file for ImagePolicyWebhook  - which should be passed via the  --admission-control-config-file=/etc/kubernetes/admission/imagepolicyconfig.json flag. It holds the location of the webhook kubeconfig file as well as a default behaviour of ImagePolicyWebhook validating admission controller to allow scheduling pods in case the Webhook is unreachable. I have set it as false, which means if the webhook is down - no container can be scheduled.

```
ubuntu@ip-172-31-22-219:/etc/kubernetes/admission$ cat imagepolicyconfig.json
{
  "imagePolicy": {
     "kubeConfigFile": "/etc/kubernetes/admission/imagepolicyconfig.yaml",
     "allowTTL": 50,
     "denyTTL": 50,
     "retryBackoff": 500,
     "defaultAllow": false
  }
}


```

Enable ImagePolicyWebhook at API Server admission controller and attach the configuration json file for ImagePolicyWebhook, 

-Create a directory in Master node named /etc/kubernetes/admission/ and put all the necessary files needed for the ImagePolicyWebhook to run. These would be imagepolicyconfig.yaml, imagepolicyconfig.yaml, webhook.pem, api-server-client.pem & api-server-client-key.pem.
- Attach it to API server pod as a hostPath based volume mount. Then we mount the volume to pod at /etc/kubernetes/admission 
- Add ImagePolicyWebhook to command line argument (via enable-admission-plugins parameter)
- Add path to JSON file (via --admission-control-config-file parameter)


```
 - hostPath:
      path: /etc/kubernetes/admission
      type: DirectoryOrCreate
    name: swararoy-vol

 - mountPath: /etc/kubernetes/admission
      name: swararoy-vol
      readOnly: true

 containers:
  - command:
    - kube-apiserver
    - --advertise-address=172.31.22.219
    - --allow-privileged=true
    - --authorization-mode=Node,RBAC
    - --client-ca-file=/etc/kubernetes/pki/ca.crt
    - --admission-control-config-file=/etc/kubernetes/admission/imagepolicyconfig.json
    - --enable-admission-plugins=NodeRestriction,PodSecurityPolicy,ImagePolicyWebhook

```

This will restart Kube API server pod and just check with a sample kubectl get nodes to check API server has been brought up.




### 01.C Test Scenario
---

1. Try to run an image from a repostory from Docker and not using LATEST tag - it should pass
2. Try to run an image from a repostory from Docker and using LATEST tag - it should fail
3. Try to run an image from a repostory from Quay and not using LATEST tag - it should fail

                                      
                                                      
