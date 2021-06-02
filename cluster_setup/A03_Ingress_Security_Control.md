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
echo "Application Service [A]" > index.html

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


