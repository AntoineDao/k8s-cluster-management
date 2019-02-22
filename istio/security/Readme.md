# Istio Security
Security in Istio determine security rules within the service mesh. In depth documentation can be found [here](https://istio.io/docs/tasks/security/). Such rules include:
* **mTLS**: Whether or not mesh traffic is encrypted
* **End User Auth**: Which services require end user authentication and how to authenticate users

## mTLS
Mutual TLS encryption is supported by default when implementing the `/istio-1.0.5/install/kubernetes/istio-demo-auth.yaml` file.

The state of mTLS can be verified for individual services by typing the following command:
> istioctl authn tls-check service-name.svc-namespace.svc.cluster.local 

More info on checking mTLS can be found [here](https://istio.io/docs/tasks/security/mutual-tls/#verify-mutual-tls-configuration).

## End User Authentication
Rather than having each individual microservice implement it's own authentication code, it is easier to set authentication rules at the service-mesh level. Istio currently supports JWT authentication and requires a JWT issuer address (http reachable endpoint containing the public decryption key of a given JWT issuer). 

In this scenario we choose to implement this policy on a namespace level where all services requiring authentication will be grouped. This namespace is called `api-private`.

The policy can be applied at namespace level by running the following command:
> kubectl apply -f end-user-auth.yml
