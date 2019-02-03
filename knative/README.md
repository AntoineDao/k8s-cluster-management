# Knative
Knative is a serverless management platform on top of kubernetes and Istio. Here's a good video to explain what it does: https://www.ibm.com/blogs/bluemix/2018/12/video-what-is-knative/

## Installations
The install is very straightforward and can be resumed to the command below (once istio is installed):

### 1. Install Istio
First install istio.
```console
kubectl apply --filename https://github.com/knative/serving/releases/download/v0.3.0/istio-crds.yaml && \
kubectl apply --filename https://github.com/knative/serving/releases/download/v0.3.0/istio.yaml
```
For some reason this deployment requires 3 pilot pods which require 2Gi memory each and were therefore unschedulable on 4Gi ram machines. I changed the config to only require 2 for now. We'll see where this gets us.

Ensure that the default namespace has istio-injection enabled (will probably add this to the API namesapce).
```console
kubectl label namespace default istio-injection=enabled
```

### 2. Install Knative
```console
kubectl apply --filename https://github.com/knative/serving/releases/download/v0.3.0/serving.yaml \
--filename https://github.com/knative/build/releases/download/v0.3.0/release.yaml \
--filename https://github.com/knative/eventing/releases/download/v0.3.0/release.yaml \
--filename https://github.com/knative/eventing-sources/releases/download/v0.3.0/release.yaml
```
For a full explanation you can check [this](https://github.com/knative/docs/blob/master/install/Knative-with-any-k8s.md) out.


### 3. Setup External DNS
This is a manual process to link the Knative loadbalancer (different from the istio one...) to a DNS record and the internal domain name we are going to create. First find the external IP of the loadbalancer so you can match it to the correct DNS record:

```console
kubectl get svc --all-namespaces | grep knative-ingressgateway

istio-system       knative-ingressgateway       LoadBalancer   10.245.74.215    157.230.64.205   80:32380/TCP,443:32390/TCP,31400:32400/TCP,15011:32525/TCP,8060:30021/TCP,853:31537/TCP,15030:30137/TCP,15031:31203/TCP   15m
```

Create an A record with that IP address and then set the internal default domain used by knative as demonstrated below.

```console
knctl domain create --default --domain api.pollination.cloud
```

Most of the info for this can be found [here](https://starkandwayne.com/blog/adding-a-custom-domain-for-knative-services/)


### Serving
Serving management info can be found [here](https://github.com/knative/docs/tree/master/serving).
API Tokens to interact with the serving API using the correct role can be acquired as demonstrated below:
> TOKEN=$(kubectl get secrets -n knative-serving -o jsonpath="{.items[?(@.metadata.annotations['kubernetes\.io/service-account\.name']=='controller')].data.token}"|base64 -d)


## Logging and Tracing
Logging/tracing are first class citizens in Knative with favoured backends using Prometheus for machine logs, elastic for traces and process logs. Setting this up is explained [here](https://github.com/knative/docs/blob/master/serving/installing-logging-metrics-traces.md)


## OpenFaas? 
https://triggermesh.com/2018/11/07/deploying-openfaas-functions-with-knative/

## Cli Utility - knctl
[github repo](https://github.com/cppforlife/knctl)
[install instructions](https://github.com/cppforlife/knctl/blob/master/docs/basic-workflow.md)

## Creating a simple app
The getting started [docs](https://github.com/knative/docs) are pretty thorough. The [go rest API example](https://github.com/knative/docs/tree/master/serving/samples/rest-api-go) is particularly good.
### Build


### Serve
[API Reference](https://github.com/knative/docs/blob/master/reference/serving.md)
