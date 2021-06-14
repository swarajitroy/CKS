# Kubernetes Audit Logging


Now enable auditing in this kubernetes cluster. Create a new policy file that will only log events based on the below specifications:

## Example Audit Requirement 
---

Any pod **deleted** from a namespace called **production** should be audited in a log file - located in host at /var/log/swararoy-kube-audit/kubeprod-audit.yaml



Namespace: prod
Operations: delete
Resources: secrets
Log Path: /var/log/prod-secrets.log
Audit file location: /etc/kubernetes/prod-audit.yaml
Maximum days to keep the logs: 30

Once the policy is created it, enable and make sure that it works.


apiVersion: audit.k8s.io/v1
kind: Policy
rules:
- level: Metadata
  namespace: ["prod"]
  verb: ["delete"]
  resources:
  - group: ""
    resource: ["secrets"]


- --audit-policy-file=/etc/kubernetes/prod-audit.yaml
- --audit-log-path=/var/log/prod-secrets.log
- --audit-log-maxage=30

- name: audit
    hostPath:
      path: /etc/kubernetes/prod-audit.yaml
      type: File

  - name: audit-log
    hostPath:
      path: /var/log/prod-secrets.log
      type: FileOrCreate


- mountPath: /etc/kubernetes/prod-audit.yaml
    name: audit
    readOnly: true
  - mountPath: /var/log/prod-secrets.log
    name: audit-log
    readOnly: false
