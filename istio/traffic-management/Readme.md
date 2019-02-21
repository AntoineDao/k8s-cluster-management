# Istio Traffic Management
Traffic Management in Istio determine traffic rules within the service mesh. More documentation can be found [here](https://istio.io/docs/tasks/traffic-management/). Such rules include:
* **Ingress**: How traffic enters the service mesh
* **Egress**: What traffic can leave the service mesh

## Ingress
We have set up a simple gateway for the `api.pollination.cloud` which redirects any non-encrypted traffic to port 443 (https) and can serve as an entrypoint to any service through `VirtualService` objects. Be sure to configure a [certificate manager](../../cert-manager) before setting up any ingress.

Once everything is ready you can configure ingress into the service mesh by running:
> kubectl apply -f ingress.yml

## Egress
The `egress.yml` file contains external traffic policies for the mesh. In this case we allow services inside the mesh to access the following external services:
* **DigitalOcean API***: In order to faciltiate cluster automation from within (eg: node scaling or external DNS management)
* **Postgres Database Server**: The external database 
* **Object Storage Server**: The external Object Storage service
