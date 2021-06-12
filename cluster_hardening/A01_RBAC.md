# Role Base Access Control (RBAC)

The default permissions for a service account don't allow it to list or modify any resources. The default service account isn't allowed to view cluster state let alone modify it in any way. By default, the default service account in a namespace has no permissions other than those of an unauthenticated user.

lets create a service account, run a pod with the newly created service account and attempt to call some Kubernetes API. 

