# Protect node metadata and endpoints

In most of the public cloud environments - the VM nodes in which Kubernetes nodes (Master, Worker) runs - will have acccess to VM Node metadata services. For example, in AWS, the IP address 169.254.169.254 is a link-local address and is valid only from the EC2 instance and runs a REST endpoint to query instance meta data. While it might be okay for the admin of the node (at OS level) to be able to query this, but a container running on the node, should not have much reason to do so. 

Lets consider a Kubernetes cluster setup by kubeadm tool on AWS (default VPC) - with one master node and one worker node.

```
ubuntu@ip-172-31-22-219:~$ kubectl get nodes -o wide
NAME               STATUS   ROLES                  AGE   VERSION   INTERNAL-IP     EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION   CONTAINER-RUNTIME
ip-172-31-17-89    Ready    <none>                 51d   v1.21.0   172.31.17.89    <none>        Ubuntu 18.04.5 LTS   5.4.0-1048-aws   cri-o://1.20.2
ip-172-31-22-219   Ready    control-plane,master   51d   v1.21.0   172.31.22.219   <none>        Ubuntu 18.04.5 LTS   5.4.0-1048-aws   cri-o://1.20.2

```
We can SSH to either the master or worker node and run the following command, and get a response

```
ubuntu@ip-172-31-22-219:~$ curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/ami-id
ami-01e7ca2ef94a0ae86
```
Now - lets run a container. 

```
ubuntu@ip-172-31-22-219:~$ kubectl run testpod --image nginx
pod/testpod created

ubuntu@ip-172-31-22-219:~$ kubectl get pods -o wide
NAME      READY   STATUS    RESTARTS   AGE   IP          NODE              NOMINATED NODE   READINESS GATES
testpod   1/1     Running   0          70s   10.44.0.1   ip-172-31-17-89   <none>           <none>

```
A container is eventually nothing but an UNIX process, we can SSH to the workder node, and check it

```
ubuntu@ip-172-31-17-89:~$ ps -aef | grep nginx
root      7513  7482  0 16:02 ?        00:00:00 nginx: master process nginx -g daemon off;
systemd+  7574  7513  0 16:02 ?        00:00:00 nginx: worker process
systemd+  7576  7513  0 16:02 ?        00:00:00 nginx: worker process

```
Now - its possible to exec to the container and ask the meta data service about the AMI ID of the instance,

```
ubuntu@ip-172-31-22-219:~$ kubectl exec -it testpod -- /bin/sh
# 
curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/ami-id
ami-01e7ca2ef94a0ae86

```
So the pod is able to reach the node meta data endpoint. 

One way to protect this would be to use Kubernetes Network Policies to stop the pods to egress to 169.254.169.254. We can do couple of things, 

- Apply a network policy to stop any pods to egress to 169.254.169.254
- Update the network policy to stop all pods egress to 169.254.169.254 except the ones which has a label named "allow-node-metadata-lookup" 


