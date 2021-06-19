
```
ubuntu@ip-172-31-22-219:~$ kubectl get nodes -o wide
NAME               STATUS   ROLES                  AGE   VERSION   INTERNAL-IP     EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION   CONTAINER-RUNTIME
ip-172-31-17-89    Ready    <none>                 70d   v1.21.0   172.31.17.89    <none>        Ubuntu 18.04.5 LTS   5.4.0-1049-aws   cri-o://1.20.2
ip-172-31-22-219   Ready    control-plane,master   70d   v1.21.0   172.31.22.219   <none>        Ubuntu 18.04.5 LTS   5.4.0-1049-aws   cri-o://1.20.2

ubuntu@ip-172-31-22-219:~$ wget https://dl.k8s.io/v1.21.2/kubernetes-client-darwin-amd64.tar.gz
--2021-06-19 18:27:41--  https://dl.k8s.io/v1.21.2/kubernetes-client-darwin-amd64.tar.gz
Resolving dl.k8s.io (dl.k8s.io)... 34.107.204.206, 2600:1901:0:26f3::
Connecting to dl.k8s.io (dl.k8s.io)|34.107.204.206|:443... connected.
HTTP request sent, awaiting response... 302 Moved Temporarily
Location: https://storage.googleapis.com/kubernetes-release/release/v1.21.2/kubernetes-client-darwin-amd64.tar.gz [following]
--2021-06-19 18:27:41--  https://storage.googleapis.com/kubernetes-release/release/v1.21.2/kubernetes-client-darwin-amd64.tar.gz
Resolving storage.googleapis.com (storage.googleapis.com)... 142.250.190.16, 142.250.190.48, 142.250.190.80, ...
Connecting to storage.googleapis.com (storage.googleapis.com)|142.250.190.16|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 26887005 (26M) [application/x-tar]
Saving to: ‘kubernetes-client-darwin-amd64.tar.gz’

```

SHA 512 bit digest as per web site 
6245ebb73ac4215420f1453906ac7a63b25954dd89a40c6f99b88cede14016d106226e6284fdc6f0634016ee7d82a8cffed424eff2b4e06a77e8e1c26d4d6759

```
ubuntu@ip-172-31-22-219:~$ sha512sum kubernetes-client-darwin-amd64.tar.gz
6245ebb73ac4215420f1453906ac7a63b25954dd89a40c6f99b88cede14016d106226e6284fdc6f0634016ee7d82a8cffed424eff2b4e06a77e8e1c26d4d6759  

```
