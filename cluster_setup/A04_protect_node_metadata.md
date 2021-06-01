# Protect node metadata and endpoints

In most of the public cloud environments - the VM nodes in which Kubernetes nodes (Master, Worker) runs - will have acccess to VM Node metadata services. For example, in AWS, the IP address 169.254.169.254 is a link-local address and is valid only from the EC2 instance and runs a REST endpoint to query instance meta data. While it might be okay for the admin of the node (at OS level) to be able to query this, but a container running on the node, should not have much reason to do so. 
