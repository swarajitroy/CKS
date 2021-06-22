# Minimize Kubernetes Dashboard GUI Persmissions


## 01. Install & Expose Kuberenets Dashboard
---

Kubernetes Dashboard can be installed via the following YAML manifests. We then additionally create a node port service (not a good practice) to access the dashboard. One of the better way to expose this is via kubectl proxy. 

```

ubuntu@ip-172-31-22-219:~$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.2.0/aio/deploy/recommended.yaml
namespace/kubernetes-dashboard created
serviceaccount/kubernetes-dashboard created
service/kubernetes-dashboard created
secret/kubernetes-dashboard-certs created
secret/kubernetes-dashboard-csrf created
secret/kubernetes-dashboard-key-holder created
configmap/kubernetes-dashboard-settings created
role.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard created
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
deployment.apps/kubernetes-dashboard created
service/dashboard-metrics-scraper created
deployment.apps/dashboard-metrics-scraper created

ubuntu@ip-172-31-22-219:~$ kubectl get ns
NAME                   STATUS   AGE
default                Active   70d
earth                  Active   16d
ingress-nginx          Active   17d
jupiter                Active   7d
kube-node-lease        Active   70d
kube-public            Active   70d
kube-system            Active   70d
kubernetes-dashboard   Active   3m10s
mars                   Active   16d
production             Active   5d

ubuntu@ip-172-31-22-219:~$ kubectl get svc -n kubernetes-dashboard
NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
dashboard-metrics-scraper   ClusterIP   10.99.166.12     <none>        8000/TCP         47h
kubernetes-dashboard        ClusterIP   10.97.131.208    <none>        443/TCP          47h
kubernetes-dashboard-2      NodePort    10.110.226.114   <none>        8443:30007/TCP   46h
ubuntu@ip-172-31-22-219:~$ kubectl get pods -n kubernetes-dashboard
NAME                                         READY   STATUS    RESTARTS   AGE
dashboard-metrics-scraper-856586f554-jnqlf   1/1     Running   0          36h
kubernetes-dashboard-78c79f97b4-nmgkt        1/1     Running   0          36h



```

![Kubernetes Dashboard](https://github.com/swarajitroy/CKS/blob/main/kubernetes-dashboard.png)


## Create a Service Account and only allow View Pods 
---

```
ubuntu@ip-172-31-22-219:~/rbac-practice$ kubectl describe sa guiviewer
Name:                guiviewer
Namespace:           default
Labels:              <none>
Annotations:         <none>
Image pull secrets:  <none>
Mountable secrets:   guiviewer-token-kf5h6
Tokens:              guiviewer-token-kf5h6
Events:              <none>

ubuntu@ip-172-31-22-219:~/rbac-practice$ cat pod-reader-role.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["get", "watch", "list"]

ubuntu@ip-172-31-22-219:~/rbac-practice$ kubectl create -f pod-reader-role.yaml
role.rbac.authorization.k8s.io/pod-reader created
ubuntu@ip-172-31-22-219:~/rbac-practice$ kubectl get role
NAME                CREATED AT
pod-reader          2021-06-21T18:14:52Z

ubuntu@ip-172-31-22-219:~/rbac-practice$ cat pod-reader-rolebinding.yaml
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
roleRef:
  # "roleRef" specifies the binding to a Role / ClusterRole
  kind: Role #this must be Role or ClusterRole
  name: pod-reader # this must match the name of the Role or ClusterRole you wish to bind to
  apiGroup: rbac.authorization.k8s.io

ubuntu@ip-172-31-22-219:~/rbac-practice$ kubectl get rolebindings
NAME                        ROLE                     AGE
read-pods                   Role/pod-reader          10s

ubuntu@ip-172-31-22-219:~$ kubectl auth can-i get  pods --as system:serviceaccount:default:guiviewer
yes
ubuntu@ip-172-31-22-219:~$ kubectl auth can-i get  deployments --as system:serviceaccount:default:guiviewer
no

```
