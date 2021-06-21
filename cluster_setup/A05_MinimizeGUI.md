kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.2.0/aio/deploy/recommended.yaml

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
