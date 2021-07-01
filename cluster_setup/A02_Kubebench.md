# Kube-bench

The Center for Internet Security (CIS) lays down guidelines and benchmarks for secure software development and maintenance. They have also created guidelines and benchmark tests for securing Kubernetes and achieve a level of Hardening for your cluster.

Kube Bench is an open-source Go application that runs the CIS Kubernetes Benchmark tests on your cluster to ensure that it meets the CIS guidelines for security.
It is particularly helpful if you are using a Vanilla Kubernetes installation on-premise or on VMs. It is also useful to understand node security in your managed clusters, like AKS, EKS, or GKE.

The managed Kubernetes clusters provide a level of CIS hardening already in place, and it delegates some settings to the user. It works best when you want to understand whether your workloads and the worker nodes are appropriately set up to implement these guidelines

```
ubuntu@ip-172-31-22-219:~$ ./kube-bench version
0.3.1

ubuntu@ip-172-31-22-219:~$ sudo ./kube-bench --benchmark cis-1.5 --config-dir `pwd`/cfg --config `pwd`/cfg/config.yaml

[INFO] 1 Master Node Security Configuration
[INFO] 1.1 Master Node Configuration Files
[PASS] 1.1.1 Ensure that the API server pod specification file permissions are set to 644 or more restrictive (Scored)
[PASS] 1.1.2 Ensure that the API server pod specification file ownership is set to root:root (Scored)
[PASS] 1.1.3 Ensure that the controller manager pod specification file permissions are set to 644 or more restrictive (Scored)
[PASS] 1.1.4 Ensure that the controller manager pod specification file ownership is set to root:root (Scored)
[PASS] 1.1.5 Ensure that the scheduler pod specification file permissions are set to 644 or more restrictive (Scored)
[PASS] 1.1.6 Ensure that the scheduler pod specification file ownership is set to root:root (Scored)
[PASS] 1.1.7 Ensure that the etcd pod specification file permissions are set to 644 or more restrictive (Scored)
[PASS] 1.1.8 Ensure that the etcd pod specification file ownership is set to root:root (Scored)
[WARN] 1.1.9 Ensure that the Container Network Interface file permissions are set to 644 or more restrictive (Not Scored)
[WARN] 1.1.10 Ensure that the Container Network Interface file ownership is set to root:root (Not Scored)
[PASS] 1.1.11 Ensure that the etcd data directory permissions are set to 700 or more restrictive (Scored)
[FAIL] 1.1.12 Ensure that the etcd data directory ownership is set to etcd:etcd (Scored)
[PASS] 1.1.13 Ensure that the admin.conf file permissions are set to 644 or more restrictive (Scored)
[PASS] 1.1.14 Ensure that the admin.conf file ownership is set to root:root (Scored)
[PASS] 1.1.15 Ensure that the scheduler.conf file permissions are set to 644 or more restrictive (Scored)
[PASS] 1.1.16 Ensure that the scheduler.conf file ownership is set to root:root (Scored)
[PASS] 1.1.17 Ensure that the controller-manager.conf file permissions are set to 644 or more restrictive (Scored)
[PASS] 1.1.18 Ensure that the controller-manager.conf file ownership is set to root:root (Scored)
[WARN] 1.1.19 Ensure that the Kubernetes PKI directory and file ownership is set to root:root (Scored)
[WARN] 1.1.20 Ensure that the Kubernetes PKI certificate file permissions are set to 644 or more restrictive (Scored)
[WARN] 1.1.21 Ensure that the Kubernetes PKI key file permissions are set to 600 (Scored)
[INFO] 1.2 API Server
[WARN] 1.2.1 Ensure that the --anonymous-auth argument is set to false (Not Scored)
[PASS] 1.2.2 Ensure that the --basic-auth-file argument is not set (Scored)
[PASS] 1.2.3 Ensure that the --token-auth-file parameter is not set (Scored)
[PASS] 1.2.4 Ensure that the --kubelet-https argument is set to true (Scored)
[PASS] 1.2.5 Ensure that the --kubelet-client-certificate and --kubelet-client-key arguments are set as appropriate (Scored)
[FAIL] 1.2.6 Ensure that the --kubelet-certificate-authority argument is set as appropriate (Scored)
[PASS] 1.2.7 Ensure that the --authorization-mode argument is not set to AlwaysAllow (Scored)
[PASS] 1.2.8 Ensure that the --authorization-mode argument includes Node (Scored)
[PASS] 1.2.9 Ensure that the --authorization-mode argument includes RBAC (Scored)
[WARN] 1.2.10 Ensure that the admission control plugin EventRateLimit is set (Not Scored)
[PASS] 1.2.11 Ensure that the admission control plugin AlwaysAdmit is not set (Scored)
[WARN] 1.2.12 Ensure that the admission control plugin AlwaysPullImages is set (Not Scored)
[WARN] 1.2.13 Ensure that the admission control plugin SecurityContextDeny is set if PodSecurityPolicy is not used (Not Scored)
[PASS] 1.2.14 Ensure that the admission control plugin ServiceAccount is set (Scored)
[PASS] 1.2.15 Ensure that the admission control plugin NamespaceLifecycle is set (Scored)
[FAIL] 1.2.16 Ensure that the admission control plugin PodSecurityPolicy is set (Scored)
[PASS] 1.2.17 Ensure that the admission control plugin NodeRestriction is set (Scored)
[PASS] 1.2.18 Ensure that the --insecure-bind-address argument is not set (Scored)
[PASS] 1.2.19 Ensure that the --insecure-port argument is set to 0 (Scored)
[PASS] 1.2.20 Ensure that the --secure-port argument is not set to 0 (Scored)
[FAIL] 1.2.21 Ensure that the --profiling argument is set to false (Scored)
[FAIL] 1.2.22 Ensure that the --audit-log-path argument is set (Scored)
[FAIL] 1.2.23 Ensure that the --audit-log-maxage argument is set to 30 or as appropriate (Scored)
[FAIL] 1.2.24 Ensure that the --audit-log-maxbackup argument is set to 10 or as appropriate (Scored)
[FAIL] 1.2.25 Ensure that the --audit-log-maxsize argument is set to 100 or as appropriate (Scored)
[PASS] 1.2.26 Ensure that the --request-timeout argument is set as appropriate (Scored)
[PASS] 1.2.27 Ensure that the --service-account-lookup argument is set to true (Scored)
[PASS] 1.2.28 Ensure that the --service-account-key-file argument is set as appropriate (Scored)
[PASS] 1.2.29 Ensure that the --etcd-certfile and --etcd-keyfile arguments are set as appropriate (Scored)
[PASS] 1.2.30 Ensure that the --tls-cert-file and --tls-private-key-file arguments are set as appropriate (Scored)
[PASS] 1.2.31 Ensure that the --client-ca-file argument is set as appropriate (Scored)
[PASS] 1.2.32 Ensure that the --etcd-cafile argument is set as appropriate (Scored)
[PASS] 1.2.33 Ensure that the --encryption-provider-config argument is set as appropriate (Scored)
[WARN] 1.2.34 Ensure that encryption providers are appropriately configured (Scored)
[WARN] 1.2.35 Ensure that the API Server only makes use of Strong Cryptographic Ciphers (Not Scored)
[INFO] 1.3 Controller Manager
[FAIL] 1.3.1 Ensure that the --terminated-pod-gc-threshold argument is set as appropriate (Scored)
[FAIL] 1.3.2 Ensure that the --profiling argument is set to false (Scored)
[PASS] 1.3.3 Ensure that the --use-service-account-credentials argument is set to true (Scored)
[PASS] 1.3.4 Ensure that the --service-account-private-key-file argument is set as appropriate (Scored)
[PASS] 1.3.5 Ensure that the --root-ca-file argument is set as appropriate (Scored)
[FAIL] 1.3.6 Ensure that the RotateKubeletServerCertificate argument is set to true (Scored)
[PASS] 1.3.7 Ensure that the --bind-address argument is set to 127.0.0.1 (Scored)
[INFO] 1.4 Scheduler
[FAIL] 1.4.1 Ensure that the --profiling argument is set to false (Scored)
[PASS] 1.4.2 Ensure that the --bind-address argument is set to 127.0.0.1 (Scored)

== Remediations ==
1.1.9 Run the below command (based on the file location on your system) on the master node.
For example,
chmod 644 <path/to/cni/files>

1.1.10 Run the below command (based on the file location on your system) on the master node.
For example,
chown root:root <path/to/cni/files>

1.1.12 On the etcd server node, get the etcd data directory, passed as an argument --data-dir,
from the below command:
ps -ef | grep etcd
Run the below command (based on the etcd data directory found above).
For example, chown etcd:etcd /var/lib/etcd

1.1.19 Run the below command (based on the file location on your system) on the master node.
For example,
chown -R root:root /etc/kubernetes/pki/

1.1.20 Run the below command (based on the file location on your system) on the master node.
For example,
chmod -R 644 /etc/kubernetes/pki/*.crt

1.1.21 Run the below command (based on the file location on your system) on the master node.
For example,
chmod -R 600 /etc/kubernetes/pki/*.key

1.2.1 Edit the API server pod specification file /etc/kubernetes/manifests/kube-apiserver.yaml
on the master node and set the below parameter.
--anonymous-auth=false

1.2.6 Follow the Kubernetes documentation and setup the TLS connection between
the apiserver and kubelets. Then, edit the API server pod specification file
/etc/kubernetes/manifests/kube-apiserver.yaml on the master node and set the
--kubelet-certificate-authority parameter to the path to the cert file for the certificate authority.
--kubelet-certificate-authority=<ca-string>

1.2.10 Follow the Kubernetes documentation and set the desired limits in a configuration file.
Then, edit the API server pod specification file /etc/kubernetes/manifests/kube-apiserver.yaml
and set the below parameters.
--enable-admission-plugins=...,EventRateLimit,...
--admission-control-config-file=<path/to/configuration/file>

1.2.12 Edit the API server pod specification file /etc/kubernetes/manifests/kube-apiserver.yaml
on the master node and set the --enable-admission-plugins parameter to include
AlwaysPullImages.
--enable-admission-plugins=...,AlwaysPullImages,...

1.2.13 Edit the API server pod specification file /etc/kubernetes/manifests/kube-apiserver.yaml
on the master node and set the --enable-admission-plugins parameter to include
SecurityContextDeny, unless PodSecurityPolicy is already in place.
--enable-admission-plugins=...,SecurityContextDeny,...

1.2.16 Follow the documentation and create Pod Security Policy objects as per your environment.
Then, edit the API server pod specification file /etc/kubernetes/manifests/kube-apiserver.yaml
on the master node and set the --enable-admission-plugins parameter to a
value that includes PodSecurityPolicy:
--enable-admission-plugins=...,PodSecurityPolicy,...
Then restart the API Server.

1.2.21 Edit the API server pod specification file /etc/kubernetes/manifests/kube-apiserver.yaml
on the master node and set the below parameter.
--profiling=false

1.2.22 Edit the API server pod specification file /etc/kubernetes/manifests/kube-apiserver.yaml
on the master node and set the --audit-log-path parameter to a suitable path and
file where you would like audit logs to be written, for example:
--audit-log-path=/var/log/apiserver/audit.log

1.2.23 Edit the API server pod specification file /etc/kubernetes/manifests/kube-apiserver.yaml
on the master node and set the --audit-log-maxage parameter to 30 or as an appropriate number of days:
--audit-log-maxage=30

1.2.24 Edit the API server pod specification file /etc/kubernetes/manifests/kube-apiserver.yaml
on the master node and set the --audit-log-maxbackup parameter to 10 or to an appropriate
value.
--audit-log-maxbackup=10

1.2.25 Edit the API server pod specification file /etc/kubernetes/manifests/kube-apiserver.yaml
on the master node and set the --audit-log-maxsize parameter to an appropriate size in MB.
For example, to set it as 100 MB:
--audit-log-maxsize=100

1.2.34 Follow the Kubernetes documentation and configure a EncryptionConfig file.
In this file, choose aescbc, kms or secretbox as the encryption provider.

1.2.35 Edit the API server pod specification file /etc/kubernetes/manifests/kube-apiserver.yaml
on the master node and set the below parameter.
--tls-cipher-suites=TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305,TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305,TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,TLS_RSA_WITH_AES_256_GCM_SHA384,TLS_RSA_WITH_AES_128_GCM_SHA256

1.3.1 Edit the Controller Manager pod specification file /etc/kubernetes/manifests/kube-controller-manager.yaml
on the master node and set the --terminated-pod-gc-threshold to an appropriate threshold,
for example:
--terminated-pod-gc-threshold=10

1.3.2 Edit the Controller Manager pod specification file /etc/kubernetes/manifests/kube-controller-manager.yaml
on the master node and set the below parameter.
--profiling=false

1.3.6 Edit the Controller Manager pod specification file /etc/kubernetes/manifests/kube-controller-manager.yaml
on the master node and set the --feature-gates parameter to include RotateKubeletServerCertificate=true.
--feature-gates=RotateKubeletServerCertificate=true

1.4.1 Edit the Scheduler pod specification file /etc/kubernetes/manifests/kube-scheduler.yaml file
on the master node and set the below parameter.
--profiling=false


== Summary ==
42 checks PASS
12 checks FAIL
11 checks WARN
0 checks INFO
[INFO] 2 Etcd Node Configuration
[INFO] 2 Etcd Node Configuration Files
[PASS] 2.1 Ensure that the --cert-file and --key-file arguments are set as appropriate (Scored)
[PASS] 2.2 Ensure that the --client-cert-auth argument is set to true (Scored)
[PASS] 2.3 Ensure that the --auto-tls argument is not set to true (Scored)
[PASS] 2.4 Ensure that the --peer-cert-file and --peer-key-file arguments are set as appropriate (Scored)
[PASS] 2.5 Ensure that the --peer-client-cert-auth argument is set to true (Scored)
[PASS] 2.6 Ensure that the --peer-auto-tls argument is not set to true (Scored)
[PASS] 2.7 Ensure that a unique Certificate Authority is used for etcd (Not Scored)

== Summary ==
7 checks PASS
0 checks FAIL
0 checks WARN
0 checks INFO
[INFO] 3 Control Plane Configuration
[INFO] 3.1 Authentication and Authorization
[WARN] 3.1.1 Client certificate authentication should not be used for users (Not Scored)
[INFO] 3.2 Logging
[WARN] 3.2.1 Ensure that a minimal audit policy is created (Scored)
[WARN] 3.2.2 Ensure that the audit policy covers key security concerns (Not Scored)

== Remediations ==
3.1.1 Alternative mechanisms provided by Kubernetes such as the use of OIDC should be
implemented in place of client certificates.

3.2.1 Create an audit policy file for your cluster.

3.2.2 Consider modification of the audit policy in use on the cluster to include these items, at a
minimum.


== Summary ==
0 checks PASS
0 checks FAIL
3 checks WARN
0 checks INFO
[INFO] 4 Worker Node Security Configuration
[INFO] 4.1 Worker Node Configuration Files
[PASS] 4.1.1 Ensure that the kubelet service file permissions are set to 644 or more restrictive (Scored)
[PASS] 4.1.2 Ensure that the kubelet service file ownership is set to root:root (Scored)
[FAIL] 4.1.3 Ensure that the proxy kubeconfig file permissions are set to 644 or more restrictive (Scored)
[FAIL] 4.1.4 Ensure that the proxy kubeconfig file ownership is set to root:root (Scored)
[PASS] 4.1.5 Ensure that the kubelet.conf file permissions are set to 644 or more restrictive (Scored)
[PASS] 4.1.6 Ensure that the kubelet.conf file ownership is set to root:root (Scored)
[WARN] 4.1.7 Ensure that the certificate authorities file permissions are set to 644 or more restrictive (Scored)
[FAIL] 4.1.8 Ensure that the client certificate authorities file ownership is set to root:root (Scored)
[PASS] 4.1.9 Ensure that the kubelet configuration file has permissions set to 644 or more restrictive (Scored)
[PASS] 4.1.10 Ensure that the kubelet configuration file ownership is set to root:root (Scored)
[INFO] 4.2 Kubelet
[PASS] 4.2.1 Ensure that the --anonymous-auth argument is set to false (Scored)
[PASS] 4.2.2 Ensure that the --authorization-mode argument is not set to AlwaysAllow (Scored)
[PASS] 4.2.3 Ensure that the --client-ca-file argument is set as appropriate (Scored)
[FAIL] 4.2.4 Ensure that the --read-only-port argument is set to 0 (Scored)
[PASS] 4.2.5 Ensure that the --streaming-connection-idle-timeout argument is not set to 0 (Scored)
[FAIL] 4.2.6 Ensure that the --protect-kernel-defaults argument is set to true (Scored)
[PASS] 4.2.7 Ensure that the --make-iptables-util-chains argument is set to true (Scored)
[PASS] 4.2.8 Ensure that the --hostname-override argument is not set (Not Scored)
[WARN] 4.2.9 Ensure that the --event-qps argument is set to 0 or a level which ensures appropriate event capture (Not Scored)
[FAIL] 4.2.10 Ensure that the --tls-cert-file and --tls-private-key-file arguments are set as appropriate (Scored)
[PASS] 4.2.11 Ensure that the --rotate-certificates argument is not set to false (Scored)
[FAIL] 4.2.12 Ensure that the RotateKubeletServerCertificate argument is set to true (Scored)
[WARN] 4.2.13 Ensure that the Kubelet only makes use of Strong Cryptographic Ciphers (Not Scored)

== Remediations ==
4.1.3 Run the below command (based on the file location on your system) on the each worker node.
For example,
chmod 644 /etc/kubernetes/proxy.conf

4.1.4 Run the below command (based on the file location on your system) on the each worker node.
For example, chown root:root /etc/kubernetes/proxy.conf

4.1.7 audit test did not run: There are no tests
4.1.8 Run the following command to modify the ownership of the --client-ca-file.
chown root:root <filename>

4.2.4 If using a Kubelet config file, edit the file to set readOnlyPort to 0.
If using command line arguments, edit the kubelet service file
/etc/systemd/system/kubelet.service.d/10-kubeadm.conf on each worker node and
set the below parameter in KUBELET_SYSTEM_PODS_ARGS variable.
--read-only-port=0
Based on your system, restart the kubelet service. For example:
systemctl daemon-reload
systemctl restart kubelet.service

4.2.6 If using a Kubelet config file, edit the file to set protectKernelDefaults: true.
If using command line arguments, edit the kubelet service file
/etc/systemd/system/kubelet.service.d/10-kubeadm.conf on each worker node and
set the below parameter in KUBELET_SYSTEM_PODS_ARGS variable.
--protect-kernel-defaults=true
Based on your system, restart the kubelet service. For example:
systemctl daemon-reload
systemctl restart kubelet.service

4.2.9 If using a Kubelet config file, edit the file to set eventRecordQPS: to an appropriate level.
If using command line arguments, edit the kubelet service file
/etc/systemd/system/kubelet.service.d/10-kubeadm.conf on each worker node and
set the below parameter in KUBELET_SYSTEM_PODS_ARGS variable.
Based on your system, restart the kubelet service. For example:
systemctl daemon-reload
systemctl restart kubelet.service

4.2.10 If using a Kubelet config file, edit the file to set tlsCertFile to the location
of the certificate file to use to identify this Kubelet, and tlsPrivateKeyFile
to the location of the corresponding private key file.
If using command line arguments, edit the kubelet service file
/etc/systemd/system/kubelet.service.d/10-kubeadm.conf on each worker node and
set the below parameters in KUBELET_CERTIFICATE_ARGS variable.
--tls-cert-file=<path/to/tls-certificate-file>
--tls-private-key-file=<path/to/tls-key-file>
Based on your system, restart the kubelet service. For example:
systemctl daemon-reload
systemctl restart kubelet.service

4.2.12 Edit the kubelet service file /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
on each worker node and set the below parameter in KUBELET_CERTIFICATE_ARGS variable.
--feature-gates=RotateKubeletServerCertificate=true
Based on your system, restart the kubelet service. For example:
systemctl daemon-reload
systemctl restart kubelet.service

4.2.13 If using a Kubelet config file, edit the file to set TLSCipherSuites: to
TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305,TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305,TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,TLS_RSA_WITH_AES_256_GCM_SHA384,TLS_RSA_WITH_AES_128_GCM_SHA256
or to a subset of these values.
If using executable arguments, edit the kubelet service file
/etc/systemd/system/kubelet.service.d/10-kubeadm.conf on each worker node and
set the --tls-cipher-suites parameter as follows, or to a subset of these values.
--tls-cipher-suites=TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305,TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305,TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,TLS_RSA_WITH_AES_256_GCM_SHA384,TLS_RSA_WITH_AES_128_GCM_SHA256
Based on your system, restart the kubelet service. For example:
systemctl daemon-reload
systemctl restart kubelet.service


== Summary ==
13 checks PASS
7 checks FAIL
3 checks WARN
0 checks INFO
[INFO] 5 Kubernetes Policies
[INFO] 5.1 RBAC and Service Accounts
[WARN] 5.1.1 Ensure that the cluster-admin role is only used where required (Not Scored)
[WARN] 5.1.2 Minimize access to secrets (Not Scored)
[WARN] 5.1.3 Minimize wildcard use in Roles and ClusterRoles (Not Scored)
[WARN] 5.1.4 Minimize access to create pods (Not Scored)
[WARN] 5.1.5 Ensure that default service accounts are not actively used. (Scored)
[WARN] 5.1.6 Ensure that Service Account Tokens are only mounted where necessary (Not Scored)
[INFO] 5.2 Pod Security Policies
[WARN] 5.2.1 Minimize the admission of privileged containers (Not Scored)
[WARN] 5.2.2 Minimize the admission of containers wishing to share the host process ID namespace (Scored)
[WARN] 5.2.3 Minimize the admission of containers wishing to share the host IPC namespace (Scored)
[WARN] 5.2.4 Minimize the admission of containers wishing to share the host network namespace (Scored)
[WARN] 5.2.5 Minimize the admission of containers with allowPrivilegeEscalation (Scored)
[WARN] 5.2.6 Minimize the admission of root containers (Not Scored)
[WARN] 5.2.7 Minimize the admission of containers with the NET_RAW capability (Not Scored)
[WARN] 5.2.8 Minimize the admission of containers with added capabilities (Not Scored)
[WARN] 5.2.9 Minimize the admission of containers with capabilities assigned (Not Scored)
[INFO] 5.3 Network Policies and CNI
[WARN] 5.3.1 Ensure that the CNI in use supports Network Policies (Not Scored)
[WARN] 5.3.2 Ensure that all Namespaces have Network Policies defined (Scored)
[INFO] 5.4 Secrets Management
[WARN] 5.4.1 Prefer using secrets as files over secrets as environment variables (Not Scored)
[WARN] 5.4.2 Consider external secret storage (Not Scored)
[INFO] 5.5 Extensible Admission Control
[WARN] 5.5.1 Configure Image Provenance using ImagePolicyWebhook admission controller (Not Scored)
[INFO] 5.6 General Policies
[WARN] 5.6.1 Create administrative boundaries between resources using namespaces (Not Scored)
[WARN] 5.6.2 Ensure that the seccomp profile is set to docker/default in your pod definitions (Not Scored)
[WARN] 5.6.3 Apply Security Context to Your Pods and Containers (Not Scored)
[WARN] 5.6.4 The default namespace should not be used (Scored)

== Remediations ==
5.1.1 Identify all clusterrolebindings to the cluster-admin role. Check if they are used and
if they need this role or if they could use a role with fewer privileges.
Where possible, first bind users to a lower privileged role and then remove the
clusterrolebinding to the cluster-admin role :
kubectl delete clusterrolebinding [name]

5.1.2 Where possible, remove get, list and watch access to secret objects in the cluster.

5.1.3 Where possible replace any use of wildcards in clusterroles and roles with specific
objects or actions.

5.1.4 Where possible, remove create access to pod objects in the cluster.

5.1.5 Create explicit service accounts wherever a Kubernetes workload requires specific access
to the Kubernetes API server.
Modify the configuration of each default service account to include this value
automountServiceAccountToken: false

5.1.6 Modify the definition of pods and service accounts which do not need to mount service
account tokens to disable it.

5.2.1 Create a PSP as described in the Kubernetes documentation, ensuring that
the .spec.privileged field is omitted or set to false.

5.2.2 Create a PSP as described in the Kubernetes documentation, ensuring that the
.spec.hostPID field is omitted or set to false.

5.2.3 Create a PSP as described in the Kubernetes documentation, ensuring that the
.spec.hostIPC field is omitted or set to false.

5.2.4 Create a PSP as described in the Kubernetes documentation, ensuring that the
.spec.hostNetwork field is omitted or set to false.

5.2.5 Create a PSP as described in the Kubernetes documentation, ensuring that the
.spec.allowPrivilegeEscalation field is omitted or set to false.

5.2.6 Create a PSP as described in the Kubernetes documentation, ensuring that the
.spec.runAsUser.rule is set to either MustRunAsNonRoot or MustRunAs with the range of
UIDs not including 0.

5.2.7 Create a PSP as described in the Kubernetes documentation, ensuring that the
.spec.requiredDropCapabilities is set to include either NET_RAW or ALL.

5.2.8 Ensure that allowedCapabilities is not present in PSPs for the cluster unless
it is set to an empty array.

5.2.9 Review the use of capabilites in applications runnning on your cluster. Where a namespace
contains applicaions which do not require any Linux capabities to operate consider adding
a PSP which forbids the admission of containers which do not drop all capabilities.

5.3.1 If the CNI plugin in use does not support network policies, consideration should be given to
making use of a different plugin, or finding an alternate mechanism for restricting traffic
in the Kubernetes cluster.

5.3.2 Follow the documentation and create NetworkPolicy objects as you need them.

5.4.1 if possible, rewrite application code to read secrets from mounted secret files, rather than
from environment variables.

5.4.2 Refer to the secrets management options offered by your cloud provider or a third-party
secrets management solution.

5.5.1 Follow the Kubernetes documentation and setup image provenance.

5.6.1 Follow the documentation and create namespaces for objects in your deployment as you need
them.

5.6.2 Seccomp is an alpha feature currently. By default, all alpha features are disabled. So, you
would need to enable alpha features in the apiserver by passing "--feature-
gates=AllAlpha=true" argument.
Edit the /etc/kubernetes/apiserver file on the master node and set the KUBE_API_ARGS
parameter to "--feature-gates=AllAlpha=true"
KUBE_API_ARGS="--feature-gates=AllAlpha=true"
Based on your system, restart the kube-apiserver service. For example:
systemctl restart kube-apiserver.service
Use annotations to enable the docker/default seccomp profile in your pod definitions. An
example is as below:
apiVersion: v1
kind: Pod
metadata:
  name: trustworthy-pod
  annotations:
    seccomp.security.alpha.kubernetes.io/pod: docker/default
spec:
  containers:
    - name: trustworthy-container
      image: sotrustworthy:latest

5.6.3 Follow the Kubernetes documentation and apply security contexts to your pods. For a
suggested list of security contexts, you may refer to the CIS Security Benchmark for Docker
Containers.

5.6.4 Ensure that namespaces are created to allow for appropriate segregation of Kubernetes
resources and that all new resources are created in a specific namespace.



```