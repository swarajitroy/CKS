# Restrict API Accesss

All API are available at https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.21/

Lets consider reading secrets 
POST /api/v1/namespaces/{namespace}/secrets
Lets assume namespace name as restricted

```
curl https://kubernetes.default/api/v1/namespaces/restricted/secrets -H "Authorization: Bearer $(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" -k

```






## Additional reading
---

- https://nieldw.medium.com/curling-the-kubernetes-api-server-d7675cfc398c
