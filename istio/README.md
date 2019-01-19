# istio
Istio is a super useful microservice management platform which integrates nicely with kubernetes. Here is a link to their website: https://istio.io. Istio requires aslightly beefier machine than the standard mini setup (mainly higher RAM requirement) so be sure to pick a cluster with a machine of at least 4GB RAM.

## Download (latest):
Download istio to your machine. This will contain both Kubernetes deployment files and the binaries for `istioctl`, a command line tool like `kubectl` but for istio artifacts on your cluster.
```
cd ~
curl -L https://git.io/getLatestIstio | sh -
echo 'export PATH="$PATH:/home/ubuntu/istio-1.0.5/bin"' >> ~/.profile # change 1.0.5 in your version
cd istio-1.0.5 # change 1.0.5 in your version
```


## Istio install

Apply CRDs:

```
kubectl apply -f ~/istio-1.0.5/install/kubernetes/helm/istio/templates/crds.yaml
```

Wait a few seconds.

Now implement Istio with mutual TLS authentication.
```
kubectl apply -f ~/istio-1.0.5/install/kubernetes/istio-demo-auth.yaml
```

Setup automated side car injection:
```
kubectl label namespace default istio-injection=enabled
```

## Enable mTLS
To enable mTLS we must deploy the `tls-setup.yml` file.

```
kubectl apply -f tls-setup.yml
```

## Enable Egress to self
In order to authenticate clients to the different microservices we will use JWT authentication. This will be managed by the `user-service` and exposed at `http://api.bigreddot.space/users/`. As we have enabled mTLS istio will only allow egress traffic (from the cluster to the internet) for specific adresses. Therefore we add the authenticating domain's address to this egress list:

```
kubectl apply -f egress-to-self.yml
```

# Metrics and Monitoring Dashboards
Istio comes with some really useful monitoring tools that plug into the envoy sidecar to automagically monitor microservices your deploy through istio. There are a few different services and you can easily access them using `kubectl port-forward`.

## Jaeger
[Jaeger](https://www.jaegertracing.io/) follows the opentrace protocol to monitor and troubleshoot transactions in complex distributed systems. Run the command below and open `http://localhost:8886`

```
kubectl port-forward -n istio-system svc/tracing 8886:80
```

## Prometheus
[Prometheus](https://prometheus.io/) is a time series logging database/query interface that locks in quite nicely with kubernetes. I personally don't have any experience with it so am not sure how to use it just yet but it seems similar to elasticsearch. Run the command below and open `http://localhost:8887`

```
kubectl port-forward -n istio-system svc/prometheus 8887:9090
```

## Grafana
[Grafana](https://grafana.com/) is a neat dashboarding tool that istio plugs into prometheus (I think...) to monitor different aspects of the services running on kubernetes. Run the command below and open `http://localhost:8888`

```
kubectl port-forward -n istio-system svc/grafana 8888:3000
```

## Policy/Service Mesh implementation

### Mutual TLS
Create mesh policy and apply it to all services except for `kubernetes.default.svc.cluster.local` (kubernetes server).
```
kubectl apply -f tls-setup.yml
```

### End-user authentication
**Do not implement this yet** as it will also bloc access to all documentation created by flask restplus.
```
kubectl apply -f user-auth.yml
```
