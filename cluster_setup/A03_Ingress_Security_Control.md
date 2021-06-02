# Properly setup Ingress objects with Security control

## Installation of Ingress Controller 
---

There are various ingress controllers - example NGNIX Ingress Controller. There is a YAML manifest available for kubernetes clusters deployed on bare-metal with generic Linux distro(Such as CentOs, Ubuntu, which is the use case for us. Its available here, 


```
ubuntu@ip-172-31-22-219:~$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.46.0/deploy/static/provider/baremetal/deploy.yaml
namespace/ingress-nginx created
serviceaccount/ingress-nginx created
configmap/ingress-nginx-controller created
clusterrole.rbac.authorization.k8s.io/ingress-nginx created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx created
role.rbac.authorization.k8s.io/ingress-nginx created
rolebinding.rbac.authorization.k8s.io/ingress-nginx created
service/ingress-nginx-controller-admission created
service/ingress-nginx-controller created
deployment.apps/ingress-nginx-controller created
validatingwebhookconfiguration.admissionregistration.k8s.io/ingress-nginx-admission created
serviceaccount/ingress-nginx-admission created
clusterrole.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
role.rbac.authorization.k8s.io/ingress-nginx-admission created
rolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
job.batch/ingress-nginx-admission-create created
job.batch/ingress-nginx-admission-patch created

ubuntu@ip-172-31-22-219:~$ kubectl get svc -n ingress-nginx
NAME                                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
ingress-nginx-controller             NodePort    10.102.30.203   <none>        80:30996/TCP,443:31934/TCP   5m31s
ingress-nginx-controller-admission   ClusterIP   10.106.210.60   <none>        443/TCP                      5m31s


```

## Setup an application
---

We will setup a NGINX based application - with a customized index.html. It will be exposed as a ClusterIP service and based on a deployment. 

```
ubuntu@ip-172-31-22-219:~$ echo "Application Service [A]" > index.html

ubuntu@ip-172-31-22-219:~$ kubectl create configmap service-a-index-html-configmap --from-file=index.html
configmap/service-a-index-html-configmap created

ubuntu@ip-172-31-22-219:~$ kubectl describe configmap service-a-index-html-configmap
Name:         service-a-index-html-configmap
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
index.html:
----
Application Service [A]

Events:  <none>


ubuntu@ip-172-31-22-219:~$ cat deployment-a.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: application-a
  name: application-a
spec:
  replicas: 1
  selector:
    matchLabels:
      app: application-a
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: application-a
    spec:
      containers:
      - image: nginx
        name: nginx
        resources: {}
        ports:
          - containerPort: 80
        volumeMounts:
          - mountPath: /usr/share/nginx/html/index.html
            name: nginx-conf
            subPath: index.html
      volumes:
       - name: nginx-conf
         configMap:
           name: service-a-index-html-configmap
status: {}

ubuntu@ip-172-31-22-219:~$ kubectl create -f deployment-a.yaml
deployment.apps/application-a created

ubuntu@ip-172-31-22-219:~$ kubectl expose deployment application-a --port=8000 --target-port=80
service/application-a exposed

ubuntu@ip-172-31-22-219:~$ kubectl get svc
NAME            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
application-a   ClusterIP   10.102.203.151   <none>        8000/TCP   6s
kubernetes      ClusterIP   10.96.0.1        <none>        443/TCP    52d

ubuntu@ip-172-31-22-219:~$ kubectl describe svc application-a
Name:              application-a
Namespace:         default
Labels:            app=application-a
Annotations:       <none>
Selector:          app=application-a
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.102.203.151
IPs:               10.102.203.151
Port:              <unset>  8000/TCP
TargetPort:        80/TCP
Endpoints:         10.44.0.4:80
Session Affinity:  None
Events:            <none>


```

## Setup an Ingress 
---

```
ubuntu@ip-172-31-22-219:~$ cat ingress.yaml

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: application-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
   - http:
      paths:
      - path: /foo
        pathType: Prefix
        backend:
          service:
            name: application-a
            port:
              number: 8000

ubuntu@ip-172-31-22-219:~$ kubectl create -f ingress.yaml
ubuntu@ip-172-31-22-219:~$ kubectl get ingress
NAME                  CLASS    HOSTS   ADDRESS        PORTS   AGE
application-ingress   <none>   *       172.31.17.89   80      117m

ubuntu@ip-172-31-22-219:~$ kubectl describe ingress application-ingress
Name:             application-ingress
Namespace:        default
Address:          172.31.17.89
Default backend:  default-http-backend:80 (<error: endpoints "default-http-backend" not found>)
Rules:
  Host        Path  Backends
  ----        ----  --------
  *
              /foo   application-a:8000 (10.44.0.4:80)
Annotations:  nginx.ingress.kubernetes.io/rewrite-target: /
Events:       <none>


```

Now - check the port of the Ingress Controller NodePort service, we could identify its 30996 for HTTP traffic. Once we get the public IP address of the kubernetes Worker node we can test the ingress from a local laptop having internet connectivity

```
ubuntu@ip-172-31-22-219:~$ kubectl get services ingress-nginx-controller -n ingress-nginx
NAME                       TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
ingress-nginx-controller   NodePort   10.102.30.203   <none>        80:30996/TCP,443:31934/TCP   6h17m

C:\Users\SWARAJITROY>curl http://18.x.y.127:30996/foo
Application Service [A]

```

## Securing the Ingress 
---

Ideally we would like to use HTTP(S) and as you can see - the HTTPS traffic (port 443) is based on the port 31934 in my setup. We try the HTTP(s) and here is result

```
C:\Users\SWARAJITROY>curl https://18.x.y.127:31934/foo
curl: (77) schannel: next InitializeSecurityContext failed: SEC_E_UNTRUSTED_ROOT (0x80090325) - The certificate chain was issued by an authority that is not trusted.

```
So - its pretty clear - NGINX Ingress Controller does provide a certificate by default - but its a self signed one and curl does not trust it. However we could make curl accept it anyway by passing a -k parameter.

```
C:\Users\SWARAJITROY>curl https://18.116.44.127:31934/foo -k
Application Service [A]

```
Its worth taking a look into the details of this SSL handshake and certififcate by running curl is verbose mode (-v)

```
ubuntu@ip-172-31-22-219:~$ curl https://18.116.44.127:31934/foo -kv
* Server certificate:
*  subject: O=Acme Co; CN=Kubernetes Ingress Controller Fake Certificate
*  start date: Jun  2 10:33:41 2021 GMT
*  expire date: Jun  2 10:33:41 2022 GMT
*  issuer: O=Acme Co; CN=Kubernetes Ingress Controller Fake Certificate
*  SSL certificate verify result: unable to get local issuer certificate (20), continuing anyway.

Application Service [A]

```
