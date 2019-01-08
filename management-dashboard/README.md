# kubernetes Dashboard Service
Set up a Kubernetes Dashboard for easier debugging and monitoring. The deployment template used is saved under `dashboard-service.yml` for convenience.

For more info on what the Kubernetes dashboard is and how to run it check out the [github repo](https://github.com/kubernetes/dashboard).

# Setting up the dashboard

## Start dashboard

Create dashboard:
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml
```

## Create user

Create sample user (if using RBAC - on by default on new installs with kops / kubeadm):
```
kubectl create -f admin-user.yaml

```

## Get login token:
```
kubectl -n kube-system get secret | grep admin-user
kubectl -n kube-system describe secret admin-user-token-<id displayed by previous command>
```

## Create local proxy
```
kubectl proxy
```

## Login to dashboard
Go to http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/#!/login

Choose login token and enter the login token from the previous step
