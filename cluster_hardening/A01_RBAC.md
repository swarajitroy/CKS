# Role Base Access Control (RBAC)

## 01. Create a human user
---


```
ubuntu@ip-172-31-22-219:~/rbac-practice$ openssl genrsa -out xxx.key 2048
ubuntu@ip-172-31-22-219:~/rbac-practice$ openssl req -new -key swararoy.key -out xxx.csr
Can't load /home/ubuntu/.rnd into RNG
140076416000448:error:2406F079:random number generator:RAND_load_file:Cannot open file:../crypto/rand/randfile.c:88:Filename=/home/ubuntu/.rnd
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:IN
State or Province Name (full name) [Some-State]:WB
Locality Name (eg, city) []:Kolkata
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:system:basic-user
Common Name (e.g. server FQDN or YOUR name) []:xxx
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

ubuntu@ip-172-31-22-219:~/rbac-practice$ cat xxx-csr.yaml
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: xxx
spec:
  groups:
  - system:authenticated
  request: LS0tC0tLS0tCg==
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth

ubuntu@ip-172-31-22-219:~/rbac-practice$ kubectl create -f xxx-csr.yaml
certificatesigningrequest.certificates.k8s.io/xxx created

ubuntu@ip-172-31-22-219:~/rbac-practice$ kubectl get csr
NAME       AGE   SIGNERNAME                            REQUESTOR          CONDITION
xxx   20s   kubernetes.io/kube-apiserver-client   kubernetes-admin   Pending

ubuntu@ip-172-31-22-219:~/rbac-practice$ kubectl describe csr xxx
Name:               xxx
Labels:             <none>
Annotations:        <none>
CreationTimestamp:  Tue, 22 Jun 2021 18:28:12 +0000
Requesting User:    kubernetes-admin
Signer:             kubernetes.io/kube-apiserver-client
Status:             Pending
Subject:
         Common Name:          xxx
         Serial Number:
         Organization:         Internet Widgits Pty Ltd
         Organizational Unit:  system:basic-user
         Country:              IN
         Locality:             Kolkata
         Province:             WB
Events:  <none>

ubuntu@ip-172-31-22-219:~/rbac-practice$ kubectl certificate approve xxx
certificatesigningrequest.certificates.k8s.io/swararoy approved
ubuntu@ip-172-31-22-219:~/rbac-practice$ kubectl get csr
NAME       AGE     SIGNERNAME                            REQUESTOR          CONDITION
xxx   2m26s   kubernetes.io/kube-apiserver-client   kubernetes-admin   Approved,Issued

ubuntu@ip-172-31-22-219:~/rbac-practice$ kubectl get csr xxx -o jsonpath='{.status.certificate}'| base64 -d > xxx.crt
ubuntu@ip-172-31-22-219:~/rbac-practice$ cat xxx.crt
-----BEGIN CERTIFICATE-----
MIIDYzCCAkugAwIBAgIQa/PUaY//SfowrQkj7Y7GIzANBgkqhkiG9w0BAQsFADAV
MRMwEQYDVQQDEwprdWJlcm5ldGVzMB4XDTIxMDYyMjE4MjUzMVoXDTIyMDYyMjE4
MjUzMVowfjELMAkGA1UEBhMCSU4xCzAJBgNVBAgTAldCMRAwDgYDVQQHEwdLb2xr
VroZ+8lWUpYv5APXX4FgV/B5EKct12ZEqmBm8noaAMWp6ZH34Wf7MIAwegoSKyHQ
+tpR7N38C3gEqZ772KHsilXn114Nl5GkAi5PjJenw9uoorjdtmbsuDfzQRcZgvV9
C7V9lZOhevPzSKX61Mhas1SoN6byOC9zOnPUZ80UoP/RFBnZBVm4hHmqMpzAO7I1
WhMFiG+peQiK/rWSAt7169RASZttk5g/JTmaRqO4brJXxTmGpN7Ie0pkU/yby1DB
/PY+0VhGhg8ypOj8+WEV961txN1wJ3745eQKRQQI3DUpPLj964UeWuVUY2sew4Sr
/LuskcnYHA==
-----END CERTIFICATE-----

ubuntu@ip-172-31-22-219:~/rbac-practice$ kubectl config set-credentials myuser --client-key=xxx.key --client-certificate=xxxy.crt --embed-certs=true
User "myuser" set.

ubuntu@ip-172-31-22-219:~/rbac-practice$ kubectl config set-context myuser@kubernetes --cluster=kubernetes --user=myuser
Context "myuser@kubernetes" created.

ubuntu@ip-172-31-22-219:~/rbac-practice$ kubectl config current-context
kubernetes-admin@kubernetes
ubuntu@ip-172-31-22-219:~/rbac-practice$ kubectl auth can-i list pods
yes

ubuntu@ip-172-31-22-219:~/rbac-practice$ kubectl config use-context myuser@kubernetes
Switched to context "myuser@kubernetes".
ubuntu@ip-172-31-22-219:~/rbac-practice$ kubectl config current-context
myuser@kubernetes

myuser@kubernetes
ubuntu@ip-172-31-22-219:~/rbac-practice$ kubectl auth can-i list pods
no

apiVersion: rbac.authorization.k8s.io/v1
# This role binding allows "jane" to read pods in the "default" namespace.
# You need to already have a Role named "pod-reader" in that namespace.
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
# You can specify more than one "subject"
- kind: ServiceAccount
  name: guiviewer
  namespace: default
- kind: User
  name: xxx
  apiGroup: rbac.authorization.k8s.io
roleRef:
  # "roleRef" specifies the binding to a Role / ClusterRole
  kind: Role #this must be Role or ClusterRole
  name: pod-reader # this must match the name of the Role or ClusterRole you wish to bind to
  apiGroup: rbac.authorization.k8s.io


```





The default permissions for a service account don't allow it to list or modify any resources. The default service account isn't allowed to view cluster state let alone modify it in any way. By default, the default service account in a namespace has no permissions other than those of an unauthenticated user.

lets create a service account, run a pod with the newly created service account and attempt to call some Kubernetes API. 

```
ubuntu@ip-172-31-22-219:~$ kubectl get svc
NAME                    TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)         AGE
application-a           ClusterIP   10.102.203.151   <none>        8000/TCP        10d
image-bouncer-webhook   NodePort    10.105.127.49    <none>        443:30080/TCP   5d14h
kubernetes              ClusterIP   10.96.0.1        <none>        443/TCP         63d
ubuntu@ip-172-31-22-219:~$ kubectl exec -it application-a-6555d4f559-ck9pm -- /bin/sh
# curl https://kubernetes.default.svc/api/v1/namespaces/default/services
curl: (60) SSL certificate problem: unable to get local issuer certificate
More details here: https://curl.haxx.se/docs/sslcerts.html

curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the web page mentioned above.
# curl https://kubernetes.default.svc/api/v1/namespaces/default/services -k
{
  "kind": "Status",
  "apiVersion": "v1",
  "metadata": {

  },
  "status": "Failure",
  "message": "services is forbidden: User \"system:anonymous\" cannot list resource \"services\" in API group \"\" in the namespace \"default\"",
  "reason": "Forbidden",
  "details": {
    "kind": "services"
  },
  "code": 403
}#

```
https://nieldw.medium.com/curling-the-kubernetes-api-server-d7675cfc398c
  
