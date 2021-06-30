# Use Network Policy to restrict Cluster Level Access

## Use Case
---

- Lets consider we have a namespace called "leaf-namespace" where we run a 3 tier application - Frond End, Application Service and Backend DB. 
- Lets consider there is another namespace "upstream-namespace" - where an application is supposed to connect to Application Service Pod in "leaf-namespace" to send some data.
- Lets consider the Application Service pod in "leaf-namespace" is supposed to send some data to an application pod in "downstream-namespace" 

All pods can be implemented as NGINX one. 

## Implementation 
---

1. Create leaf-namespace and then a pod which has following labels application=leaf and tier=db and a name=app-db and image=nginx. 
2. Create a pod in leaf-namespace  and then a pod which has following labels application=leaf and tier=app and a name=app-service and image=nginx.
3. Create a pod in leaf-namespace  and then a pod which has following labels application=leaf and tier=frontend and a name=app-frontend and image=nginx
4. Create upstream-namespace and then a pod which has following labels application=leaf and tier=app and image=nginx
5. Create downtream-namespace and then a pod which has following labels application=leaf and tier=app and image=nginx

This is too open a setup - with all the 5 pods across 3 namespaces can communicate with each other. 

![Use cases](https://github.com/swarajitroy/CKS/blob/main/cluster_setup/20210630_222017.jpg)

## Database Pod Network Policy 
---

Lets add a Network policy that ONLY pods from leaf-namespace and having following labels application=leaf and tier=app can talk to this database pod (application=leaf and tier=db and a name=app-db and image=nginx. )

## Application Pod Network Policy 
---

- Lets add a Network policy that ONLY pods from leaf-namespace and having following labels application=leaf and tier=frontend can talk to this application  pod. 
- Also the pod  application=leaf and tier=app and image=nginx from upstream-namespace should be able to connect as well. 
- Also the application pod in leaf-namespace should be able to send data to  downtream-namespace and then a pod which has following labels application=leaf and tier=app and image=nginx
