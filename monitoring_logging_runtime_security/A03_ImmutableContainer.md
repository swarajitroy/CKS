# Immutable Container

Use readOnlyRootFilesystem as true in SecurityContext and use volume mounts with emptyDirs

```
ubuntu@ip-172-31-22-219:~/immutable-container-practice$ cat immutable-testpod.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: immutable-testpod
  name: immutable-testpod
spec:
  securityContext:
    runAsUser: 999
  containers:
  - image: busybox:1.33.1
    command: ['sh', '-c', 'echo "Hello, Kubernetes!" && sleep 3600']
    name: immutable-testpod
    volumeMounts:
    - mountPath: /tmp
      name: tmp-volume
    securityContext:
       readOnlyRootFilesystem: true
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  volumes:
  - name: tmp-volume
    emptyDir: {}
status: {}

```
