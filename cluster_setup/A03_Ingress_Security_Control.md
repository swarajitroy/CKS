# Properly setup Ingress objects with Security control

## Installation of Ingress Controller 
---

There are various ingress controllers - example NGNIX Ingress Controller. There is a YAML manifest available for kubernetes clusters deployed on bare-metal with generic Linux distro(Such as CentOs, Ubuntu, which is the use case for us. Its available here, 

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.46.0/deploy/static/provider/baremetal/deploy.yaml
