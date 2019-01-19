# kubernetes Dashboard Service

This section explains how to set up a Kubernetes Dashboard for easier debugging and monitoring. The deployment template used is saved under [`dashboard-deployment.yml`](./dashboard-deployment.yml) for convenience.

For more info on what the Kubernetes dashboard is and how to run it check out the [github repository](https://github.com/kubernetes/dashboard).

# Setting up the dashboard

## Start dashboard

To deploy Dashboard execute following command:

`kubectl apply -f dashboard-deployment.yml`

You can also use the file from Kubernetes repository:
`kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml`

```shell
$ kubectl apply -f dashboard-deployment.yml
secret/kubernetes-dashboard-certs created
serviceaccount/kubernetes-dashboard created
role.rbac.authorization.k8s.io/kubernetes-dashboard-minimal created
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard-minimal created
deployment.apps/kubernetes-dashboard created
service/kubernetes-dashboard created
```

## Create user

Create sample user (if using RBAC - on by default on new installs with kops / kubeadm):

`kubectl create -f admin-user.yaml`

```shell
$ kubectl create -f admin-user.yaml
serviceaccount/admin-user created
clusterrolebinding.rbac.authorization.k8s.io/admin-user created
```

## Get login token

```shell
$ kubectl -n kube-system get secret | grep admin-user
admin-user-token-<id>             kubernetes.io/service-account-token   3      109s
$ kubectl -n kube-system describe secret admin-user-token-<id> | grep token:
token:      <a long token string>
```

## Create local proxy

To access Dashboard from your local workstation you must create a secure channel to your Kubernetes cluster. Run the following command:

```
$ kubectl proxy
Starting to serve on 127.0.0.1:8001
```

## Login to dashboard
Go to http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/#!/login

Choose login token and enter the login token from the previous step. Congratulations! :)
