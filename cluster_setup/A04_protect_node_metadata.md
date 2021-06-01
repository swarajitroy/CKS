# Protect node metadata and endpoints

In most of the public cloud environments - the VM nodes in which Kubernetes nodes (Master, Worker) runs - will have acccess to VM Node metadata services. For example, in AWS, the IP address 169.254.169.254 is a link-local address and is valid only from the EC2 instance and runs a REST endpoint to query instance meta data. While it might be okay for the admin of the node (at OS level) to be able to query this, but a container running on the node, should not have much reason to do so. 

Lets consider a Kubernetes cluster setup by kubeadm tool on AWS (default VPC) - with one master node and one worker node.

```
ubuntu@ip-172-31-22-219:~$ kubectl get nodes -o wide
NAME               STATUS   ROLES                  AGE   VERSION   INTERNAL-IP     EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION   CONTAINER-RUNTIME
ip-172-31-17-89    Ready    <none>                 51d   v1.21.0   172.31.17.89    <none>        Ubuntu 18.04.5 LTS   5.4.0-1048-aws   cri-o://1.20.2
ip-172-31-22-219   Ready    control-plane,master   51d   v1.21.0   172.31.22.219   <none>        Ubuntu 18.04.5 LTS   5.4.0-1048-aws   cri-o://1.20.2

```
