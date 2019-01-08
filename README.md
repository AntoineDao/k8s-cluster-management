# Getting Started
This mono repo contains code to provision, manage and deploy to a kubernetes cluster. Specifically this cluster contains a Postgres database and microservices connecting to it that manage ladybug tools cloud services. 

Here is the order in which to tackle this deployment:

1. `digital-ocean-cluster` - Deploy a blank cluster on Digital Ocean
2.  `management-dashboard` - Deploy a kubernetes dashboard pod/service on the cluster to facilitate debugging
3. `istio` - Deploy istio with mTLS enabled
4. `externalDNS` - Set up an externel load balancer to provide a domain name to access your cluster's services
5. `postgres-database` - Set up a Postgres Database with a mounted volume which will presist even if the entire cluster is shut down
6. `user-service` - Set up a basic user registration/management service
7. `project-service` - Set up a basic project creation/management service

...

9. `take a break`!


# Useful Notes

## DNS debugging
Use the `busybox` pod to query DNS from inside the cluster.

> kubectl create -f https://k8s.io/examples/admin/dns/busybox.yaml

> kubectl exec -ti busybox -- nslookup kubernetes.default

## Access Postgres through psql locally

> psql -h <hostname> -U postgres --password -p <port> postgres