# Argo & Argo Events
Argo and Argo events is a suite of workflow automation tools for kubernetes. Argo offers an expressive yaml based workflow orchestration language using Docker containers while Argo Events facilitates interoperability of long lasting/complex workflow triggers.

## Installation
The [QuickStart](https://github.com/argoproj/argo-events/blob/master/docs/quickstart.md) guide on the Argo Events repo provides some good understanding of how to get going. For an even shorter TL;DR here's the essential commands to run to set up Argo Events:

### Argo Events Install
```console
kubectl create namespace argo-events
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/master/hack/k8s/manifests/argo-events-sa.yaml
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/master/hack/k8s/manifests/argo-events-cluster-roles.yaml
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/master/hack/k8s/manifests/sensor-crd.yaml
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/master/hack/k8s/manifests/gateway-crd.yaml
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/master/hack/k8s/manifests/sensor-controller-configmap.yaml
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/master/hack/k8s/manifests/sensor-controller-deployment.yaml
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/master/hack/k8s/manifests/gateway-controller-configmap.yaml
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/master/hack/k8s/manifests/gateway-controller-deployment.yaml
```

### Argo Install
```console
kubectl apply -n argo-events -f argo-install.yml # corresponds to https://raw.githubusercontent.com/argoproj/argo/v2.2.1/manifests/install.yaml with modifed namespace
kubectl create rolebinding default-admin --clusterrole=admin --serviceaccount=default:default # Creates admin role, which we might demote in future
```

### Check The UI

```console
kubectl -n argo-events port-forward deployment/argo-ui 8001:8001
```
Then visit: [http://127.0.0.1:8001](http://127.0.0.1:8001)

## Example
Here is a quick example to test that everything has been installed properly

### 1. Create a webhook gateway
```console
kubectl apply -n argo-events -f example/webhook-gateway-configmap.yml
kubectl apply -n argo-events -f example/webhook-gateway-http.yml
```

### 2. Create a Webhook Sensor
```console
kubectl apply -n argo-events -f example/webhook-sensor-http.yml
```

### 3. Trigger the Webhook and Workflow
```console
kubectl port-forward svc/webhook-gateway-svc -n argo-events 8003:80  
```
And in another terminal window
```console
curl -d '{"message":"this is my first webhook"}' -H "Content-Type: application/json" -X POST localhost:8003/foo
```

## Useful Links
* [Workflow Examples](https://github.com/argoproj/argo/blob/master/examples/README.md)
* [Event Gateway and Sensor Examples](https://github.com/argoproj/argo-events/tree/master/examples)