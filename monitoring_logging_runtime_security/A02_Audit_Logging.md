# Kubernetes Audit Logging


Now enable auditing in this kubernetes cluster. Create a new policy file that will only log events based on the below specifications:

## Example Audit Requirement 
---

Any pod **deleted** from a namespace called **production** should be audited in a log file - located in host (Master Node) at **/var/log/swararoy-kube-audit/swararoy-kube-audit.log**

## Setup Auditing in Kuberenetes
---

###  A. Create a Policy file based on Auditing Need
---

```
ubuntu@ip-172-31-22-219:/etc/kubernetes/manifests$ cat /etc/kubernetes/audit-policy/swararoy_kube_audit_policy.yaml

apiVersion: audit.k8s.io/v1 # This is required.
kind: Policy
# Don't generate audit events for all requests in RequestReceived stage.
omitStages:
rules:
  # Log pod changes at RequestResponse level
  - level: RequestResponse
    namespace: ["production"]
    verbs: ["delete"]
    resources:
    - group: ""
      # Resource "pods" doesn't match requests to any subresource of pods,
      # which is consistent with the RBAC policy.
      resources: ["pods"]

```

###  B. Create mounts for APIServer for policy file and log location
---

In Kubeadm based sets - the APIServer is setup to run as a Pod. We change the manifest file 

To create the hostpath

```
  - hostPath:
      path: /var/log/swararoy-kube-audit/
      type: DirectoryOrCreate
    name: swararoy-kube-audit-location
  - hostPath:
      path: /etc/kubernetes/audit-policy/
      type: DirectoryOrCreate
    name: swararoy-kube-audit-policy-location

```
and mount to hostpath to container file systems

```

 - mountPath: /var/log/swararoy-kube-audit/
      name: swararoy-kube-audit-location
    - mountPath: /etc/kubernetes/audit-policy/
      name: swararoy-kube-audit-policy-location
      readOnly: true


```

###  C. Update APIServer to change the Auditing
---

```
    - --audit-policy-file=/etc/kubernetes/audit-policy/swararoy_kube_audit_policy.yaml
    - --audit-log-path=/var/log/swararoy-kube-audit/swararoy-kube-audit.log


```

###  D. Test
---

Delete a pod in production namespace and check the audit log file.

```
ubuntu@ip-172-31-22-219:/etc/kubernetes/manifests$ cat /var/log/swararoy-kube-audit/swararoy-kube-audit.log

{"kind":"Event","apiVersion":"audit.k8s.io/v1","level":"RequestResponse","auditID":"1fe19559-0de2-4d09-aff4-23af76fed825","stage":"RequestReceived","requestURI":"/api/v1/namespaces/production/pods/hello-kube-audit","verb":"delete","user":{"username":"kubernetes-admin","groups":["system:masters","system:authenticated"]},"sourceIPs":["172.31.22.219"],"userAgent":"kubectl/v1.21.0 (linux/amd64) kubernetes/cb303e6","objectRef":{"resource":"pods","namespace":"production","name":"hello-kube-audit","apiVersion":"v1"},"requestReceivedTimestamp":"2021-06-14T18:34:10.297371Z","stageTimestamp":"2021-06-14T18:34:10.297371Z"}


```

