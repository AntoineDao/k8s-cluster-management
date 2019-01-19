# External DNS Setup
In order for services within the cluster to be publicly available we need to setup the DNS records. [External DNS](https://github.com/kubernetes-incubator/external-dns) provides a descent level of integration with most cloud providers to do so. In this case we are using Digital Ocean so the files provided will work for this cloud provider specifically.

1. Create the external DNS deployment which will ensure kubernetes Load Balancer objects will be added to the DNS records for the domain managed by Digital Ocean
> kubectl create -f externalDNS.yml

2. Create the internal ingress for an example nginx load balancer
> kubectl create -f nginx.yml

# Istio External DNS Setup
Connecting to an external DNS normally is cool. Doing with Istio is like cool on steroids. In order to do so you should follow the steps below. You can also get mor information on this deployment type [here](https://github.com/kubernetes-incubator/external-dns/blob/master/docs/tutorials/istio.md).

1. Create the external DNS deployment which will be in charge of creating subdomain name DNS records for the Digital Ocean Load Balancer whenever new Istio gateways are created
> kubctel create -f istio-externalDNS.yml

2. Test out this deployment by creating a simple gateway and service
> kubectl create -f istio-test/httpbin.yml
> kubectl create -f istio-test/gateway.yml
> kubectl create -f istio-test/routes.yml

To check this is working run:
> curl -I http://httpbin.yourdomainname.com/status/200

which should return

```
HTTP/1.1 200 OK
server: envoy
date: Wed, 26 Dec 2018 17:17:07 GMT
content-type: text/html; charset=utf-8
access-control-allow-origin: *
access-control-allow-credentials: true
content-length: 0
x-envoy-upstream-service-time: 1
```