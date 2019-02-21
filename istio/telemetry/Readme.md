# Istio Telemetry
Service Mesh telemetry can be managed by Istio through distributed tracing and log aggregation. Some in depth documentation can be found [here](https://istio.io/docs/tasks/telemetry/). Not much configurationhas been set up yet but quite a few monitoring services can be accessed out of the box.

## Metrics and Monitoring Dashboards
Istio comes with some really useful monitoring tools that plug into the envoy sidecar to
automagically monitor microservices your deploy through istio. There are a few different
services and you can easily access them using `kubectl port-forward`.

### Jaeger
[Jaeger](https://www.jaegertracing.io/) follows the opentrace protocol to monitor and
troubleshoot transactions in complex distributed systems. Run the command below and open
`http://localhost:8886`

```shell
$ kubectl port-forward -n istio-system svc/tracing 8886:80
```

### Prometheus
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

### Grafana
[Grafana](https://grafana.com/) is a dashboarding tool that can be used to visualize the
data collected by Prometheus. Istio uses Grafana to visualize different aspects of the
services running on kubernetes from the data collected by Prometheus. Run the command
below and open `http://localhost:8888`

```shell
$ kubectl port-forward -n istio-system svc/grafana 8888:3000
```
