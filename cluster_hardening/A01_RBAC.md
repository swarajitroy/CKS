# Role Base Access Control (RBAC)

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
  
