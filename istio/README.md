# istio
Istio is a super useful microservice management platform which integrates nicely with
kubernetes. Here is a link to their website: https://istio.io. Istio requires a slightly
beefier machine than the standard mini setup (mainly higher RAM requirement) so be sure
to pick a cluster with a machine of at least 4GB RAM.

## Download (latest):
Download istio to your machine. This will contain both Kubernetes deployment files and
the binaries for `istioctl`, a command line tool like `kubectl` but for istio artifacts
on your cluster.

```shell
$ cd ~
$ curl -L https://git.io/getLatestIstio | sh -
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100  1631  100  1631    0     0   3608      0 --:--:-- --:--:-- --:--:--  3608
Downloading istio-1.0.5 from https://github.com/istio/istio/releases/download/1.0.5/istio-1.0.5-linux.tar.gz ...
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   614    0   614    0     0   4759      0 --:--:-- --:--:-- --:--:--  4759
100 14.2M  100 14.2M    0     0  5994k      0  0:00:02  0:00:02 --:--:-- 8076k
Downloaded into istio-1.0.5:
bin  install  istio.VERSION  LICENSE  README.md  samples  tools
Add <INSTALLATION FOLDER>/bin to your path; e.g copy paste in your shell and/or ~/.profile:
export PATH="$PATH:<INSTALLATION FOLDER>/bin"

$ echo 'export PATH="$PATH:<INSTALLATION FOLDER>/bin"' >> ~/.profile
```


## Istio install

Apply CRDs:

```shell
$ kubectl apply -f ~/istio-1.0.5/install/kubernetes/helm/istio/templates/crds.yaml
```

Wait a few seconds.

Now implement Istio with mutual TLS authentication.

```shell
$ kubectl apply -f ~/istio-1.0.5/install/kubernetes/istio-demo-auth.yaml
```

Setup automated side car injection:

```shell
$ kubectl label namespace default istio-injection=enabled
```

## Enable mTLS
To enable mTLS we must deploy the `tls-setup.yml` file.

```shell
$ kubectl apply -f tls-setup.yml
```

## Enable Egress to self
In order to authenticate clients to the different microservices we will use JWT
authentication. This will be managed by the `user-service` and exposed at
`http://<domain>/users/`. As we have enabled mTLS istio will only allow egress
traffic (from the cluster to the internet) for specific adresses. Therefore we add the
authenticating domain's address to this egress list:

```shell
kubectl apply -f egress-to-self.yml
```

# Metrics and Monitoring Dashboards
Istio comes with some really useful monitoring tools that plug into the envoy sidecar to
automagically monitor microservices your deploy through istio. There are a few different
services and you can easily access them using `kubectl port-forward`.

## Jaeger
[Jaeger](https://www.jaegertracing.io/) follows the opentrace protocol to monitor and
troubleshoot transactions in complex distributed systems. Run the command below and open
`http://localhost:8886`

```shell
$ kubectl port-forward -n istio-system svc/tracing 8886:80
```

## Prometheus
[Prometheus](https://prometheus.io/) is a monitoring and alerting toolkit.

> Prometheus scrapes metrics from instrumented jobs, either directly or via an
> intermediary push gateway for short-lived jobs. It stores all scraped samples locally
> and runs rules over this data to either aggregate and record new time series from
> existing data or generate alerts. Grafana or other API consumers can be used to
> visualize the collected data."

Run the command below and then open `http://localhost:8887`

```shell
$ kubectl port-forward -n istio-system svc/prometheus 8887:9090
```

## Grafana
[Grafana](https://grafana.com/) is a dashboarding tool that can be used to visualize the
data collected by Prometheus. Istio uses Grafana to visualize different aspects of the
services running on kubernetes from the data collected by Prometheus. Run the command
below and open `http://localhost:8888`

```shell
$ kubectl port-forward -n istio-system svc/grafana 8888:3000
```

## Policy/Service Mesh implementation

### Mutual TLS
Create mesh policy and apply it to all services except for `kubernetes.default.svc.cluster.local` (kubernetes server).

```shell
kubectl apply -f tls-setup.yml
```

### End-user authentication
**Do not implement this yet** as it will also bloc access to all documentation created by flask restplus.
```
kubectl apply -f user-auth.yml
```
