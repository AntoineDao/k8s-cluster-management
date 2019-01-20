# Getting Started
This mono repo contains code to provision, manage and deploy to a kubernetes cluster. Specifically this cluster contains a Postgres database and microservices connecting to it that manage ladybug tools cloud services. 

Here is the order in which to tackle this deployment:

1. [Deploy a blank cluster on Digital Ocean](./01-digital-ocean-cluster/README.md).
2. [Deploy a kubernetes dashboard pod/service on the cluster to facilitate debugging](./02-management-dashboard/README.md).
3. [Deploy `istio` with mTLS enabled](./03-istio/README.md).
4. [Set up an externel load balancer to provide a domain name to access your cluster's services](./04-externalDNS/README.md).
5. [Set up a Postgres Database with a mounted volume which will presist even if the entire cluster is shut down](./05-postgres-database/README.md).
...
6. **take a break!**
7. Deploy the rest of the microservices as needed!
   

# Useful Notes

## DNS debugging
Use the `busybox` pod to query DNS from inside the cluster.

`kubectl create -f https://k8s.io/examples/admin/dns/busybox.yaml`

`kubectl exec -ti busybox -- nslookup kubernetes.default`

## Access Postgres through psql locally

`psql -h <hostname> -U postgres --password -p <port> postgres`
