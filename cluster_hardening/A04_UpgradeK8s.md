# Upgrade Kubernetes 

## A. Upgrade Master Node
---


### A.01. Plan the Upgrade
---

```
ubuntu@ip-172-31-22-219:~$ sudo kubeadm upgrade plan
[upgrade/versions] Cluster version: v1.21.0
[upgrade/versions] kubeadm version: v1.21.0
[upgrade/versions] Target version: v1.21.2
[upgrade/versions] Latest version in the v1.21 series: v1.21.2

Components that must be upgraded manually after you have upgraded the control plane with 'kubeadm upgrade apply':
COMPONENT   CURRENT       TARGET
kubelet     2 x v1.21.0   v1.21.2

Upgrade to the latest version in the v1.21 series:

COMPONENT                 CURRENT    TARGET
kube-apiserver            v1.21.0    v1.21.2
kube-controller-manager   v1.21.0    v1.21.2
kube-scheduler            v1.21.0    v1.21.2
kube-proxy                v1.21.0    v1.21.2
CoreDNS                   v1.8.0     v1.8.0
etcd                      3.4.13-0   3.4.13-0

You can now apply the upgrade by executing the following command:

        kubeadm upgrade apply v1.21.2

Note: Before you can perform this upgrade, you have to update kubeadm to v1.21.2.

```

We decide to upgrade to v1.21.1 and not the proposed v1.21.2.

### A.02. Drain the Master Node
---

### A.03. Upgrade kubeadm on Master Node
---

```
ubuntu@ip-172-31-22-219:~$ sudo apt-mark unhold kubeadm
Canceled hold on kubeadm.
N: Ignoring file 'devel:kubic:libcontainers:stable:cri-o:18.04.5' in directory '/etc/apt/sources.list.d/' as it has an invalid filename extension
ubuntu@ip-172-31-22-219:~$ apt-get update
Reading package lists... Done
N: Ignoring file 'devel:kubic:libcontainers:stable:cri-o:18.04.5' in directory '/etc/apt/sources.list.d/' as it has an invalid filename extension
E: Could not open lock file /var/lib/apt/lists/lock - open (13: Permission denied)
E: Unable to lock directory /var/lib/apt/lists/
W: Problem unlinking the file /var/cache/apt/pkgcache.bin - RemoveCaches (13: Permission denied)
W: Problem unlinking the file /var/cache/apt/srcpkgcache.bin - RemoveCaches (13: Permission denied)
ubuntu@ip-172-31-22-219:~$ sudo apt-get update
Hit:1 http://us-east-2.ec2.archive.ubuntu.com/ubuntu bionic InRelease
Get:2 http://us-east-2.ec2.archive.ubuntu.com/ubuntu bionic-updates InRelease [88.7 kB]
Get:3 http://us-east-2.ec2.archive.ubuntu.com/ubuntu bionic-backports InRelease [74.6 kB]
Get:4 http://security.ubuntu.com/ubuntu bionic-security InRelease [88.7 kB]
Hit:5 https://packages.cloud.google.com/apt kubernetes-xenial InRelease
Hit:6 http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/1.20/xUbuntu_18.04  InRelease
Hit:7 https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_18.04  InRelease
Fetched 252 kB in 1s (351 kB/s)
Reading package lists... Done
N: Ignoring file 'devel:kubic:libcontainers:stable:cri-o:18.04.5' in directory '/etc/apt/sources.list.d/' as it has an invalid filename extension

ubuntu@ip-172-31-22-219:~$ sudo apt-get install -y kubeadm=1.21.1-00
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following packages were automatically installed and are no longer required:
  linux-aws-5.4-headers-5.4.0-1048 linux-headers-5.4.0-1048-aws linux-image-5.4.0-1048-aws linux-modules-5.4.0-1048-aws
Use 'sudo apt autoremove' to remove them.
The following packages will be upgraded:
  kubeadm
1 upgraded, 0 newly installed, 0 to remove and 37 not upgraded.
Need to get 8985 kB of archives.
After this operation, 1876 kB of additional disk space will be used.
Get:1 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubeadm amd64 1.21.1-00 [8985 kB]
Fetched 8985 kB in 1s (13.5 MB/s)
N: Ignoring file 'devel:kubic:libcontainers:stable:cri-o:18.04.5' in directory '/etc/apt/sources.list.d/' as it has an invalid filename extension
(Reading database ... 141159 files and directories currently installed.)
Preparing to unpack .../kubeadm_1.21.1-00_amd64.deb ...
Unpacking kubeadm (1.21.1-00) over (1.21.0-00) ...
Setting up kubeadm (1.21.1-00) ...
N: Ignoring file 'devel:kubic:libcontainers:stable:cri-o:18.04.5' in directory '/etc/apt/sources.list.d/' as it has an invalid filename extension

ubuntu@ip-172-31-22-219:~$ sudo apt-mark hold kubeadm
kubeadm set on hold.
N: Ignoring file 'devel:kubic:libcontainers:stable:cri-o:18.04.5' in directory '/etc/apt/sources.list.d/' as it has an invalid filename extension

ubuntu@ip-172-31-22-219:~$ kubeadm version
kubeadm version: &version.Info{Major:"1", Minor:"21", GitVersion:"v1.21.1", GitCommit:"5e58841cce77d4bc13713ad2b91fa0d961e69192", GitTreeState:"clean", BuildDate:"2021-05-12T14:17:27Z", GoVersion:"go1.16.4", Compiler:"gc", Platform:"linux/amd64"}

```


### A.04 .Kubeadm Upgrade apply
---

```
ubuntu@ip-172-31-22-219:~$ sudo kubeadm upgrade apply v1.21.1
[upgrade/config] Making sure the configuration is correct:
[upgrade/config] Reading configuration from the cluster...
[upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
[preflight] Running pre-flight checks.
[upgrade] Running cluster health checks
[upgrade/version] You have chosen to change the cluster version to "v1.21.1"
[upgrade/versions] Cluster version: v1.21.0
[upgrade/versions] kubeadm version: v1.21.1

[upgrade/successful] SUCCESS! Your cluster was upgraded to "v1.21.1". Enjoy!

```

### A.05 . Upgrade Kubelet and Kubectl version
---
```
ubuntu@ip-172-31-22-219:~$ sudo apt-mark unhold kubelet kubectl
Canceled hold on kubelet.
Canceled hold on kubectl.

ubuntu@ip-172-31-22-219:~$ sudo apt-get update
Hit:1 http://us-east-2.ec2.archive.ubuntu.com/ubuntu bionic InRelease
Get:2 http://us-east-2.ec2.archive.ubuntu.com/ubuntu bionic-updates InRelease [88.7 kB]
Get:3 http://us-east-2.ec2.archive.ubuntu.com/ubuntu bionic-backports InRelease [74.6 kB]
Get:4 http://security.ubuntu.com/ubuntu bionic-security InRelease [88.7 kB]
Hit:6 http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/1.20/xUbuntu_18.04  InRelease
Hit:7 https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_18.04  InRelease
Hit:5 https://packages.cloud.google.com/apt kubernetes-xenial InRelease
Fetched 252 kB in 1s (377 kB/s)
Reading package lists... Done
N: Ignoring file 'devel:kubic:libcontainers:stable:cri-o:18.04.5' in directory '/etc/apt/sources.list.d/' as it has an invalid filename extension

ubuntu@ip-172-31-22-219:~$ sudo apt-get install -y kubelet=1.21.1-00 kubectl=1.21.1-00
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following packages were automatically installed and are no longer required:
  linux-aws-5.4-headers-5.4.0-1048 linux-headers-5.4.0-1048-aws linux-image-5.4.0-1048-aws linux-modules-5.4.0-1048-aws
Use 'sudo apt autoremove' to remove them.
The following packages will be upgraded:
  kubectl kubelet
2 upgraded, 0 newly installed, 0 to remove and 36 not upgraded.
Need to get 28.0 MB of archives.
After this operation, 1167 kB of additional disk space will be used.
Get:1 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubectl amd64 1.21.1-00 [9225 kB]
Get:2 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubelet amd64 1.21.1-00 [18.8 MB]
Fetched 28.0 MB in 2s (15.0 MB/s)
N: Ignoring file 'devel:kubic:libcontainers:stable:cri-o:18.04.5' in directory '/etc/apt/sources.list.d/' as it has an invalid filename extension
(Reading database ... 141159 files and directories currently installed.)
Preparing to unpack .../kubectl_1.21.1-00_amd64.deb ...
Unpacking kubectl (1.21.1-00) over (1.21.0-00) ...
Preparing to unpack .../kubelet_1.21.1-00_amd64.deb ...
Unpacking kubelet (1.21.1-00) over (1.21.0-00) ...
Setting up kubelet (1.21.1-00) ...
Setting up kubectl (1.21.1-00) ...
N: Ignoring file 'devel:kubic:libcontainers:stable:cri-o:18.04.5' in directory '/etc/apt/sources.list.d/' as it has an invalid filename extension

ubuntu@ip-172-31-22-219:~$ sudo apt-mark hold kubelet kubectl
kubelet set on hold.
kubectl set on hold.

```

### A.06 . Uncordon Master Node
---

```
ubuntu@ip-172-31-22-219:~$ kubectl get nodes
NAME               STATUS   ROLES                  AGE   VERSION
ip-172-31-17-89    Ready    <none>                 74d   v1.21.0
ip-172-31-22-219   Ready    control-plane,master   74d   v1.21.1

```

### A.07 . Uncordon Master Node
---

## B. Upgrade Worker Node
---

### B.01. Drain the Master Node
---

### B.02. Upgrade kubeadm on Master Node
---

### B.03. Kubeadm Upgrade Node
---
### B.04. Upgrade Kubelet version
---

### B.05. Uncordon Worker Node
---

