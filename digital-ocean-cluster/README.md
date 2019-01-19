# Digital Ocean setup

We can use the Digital Ocean API to generate and scale new kubernetes clusters. To do
this we will install the Digital Ocean API client `doctl` by following [these
instructions](https://github.com/digitalocean/doctl/blob/master/README.md). As of Jan
2019 we are using version  1.12.2 which has beta support of kubernetes. You can see the
version by typing `doctl version` after installing `doctl`.

```console
$ doctl version
doctl version 1.12.2-release
Git commit hash: 74b1791e
```

We enable beta support by running the following command:

`export DIGITALOCEAN_ENABLE_BETA=1`

Make sure to have a Digitial Ocean authentication token handy by visitng [this page](https://cloud.digitalocean.com/account/api/tokens) and run the following commands to autenticate your client:

```console
$ doctl auth init
DigitalOcean access token: <Paste your token here and press Enter>

Validating token... OK
```
Now we can spin up a kubernetes cluster programatically using
`doctl kubernetes cluster create` command. The example below creates a cluster named
`ladybug` with 2 machines of size `c-2` (2 vcpus and 4GB ram each) in London (`--region lon1`) 
and waits for the created cluster to become running.

To see usage options and full list of flags you can try `doctl k8s cluster create --help`.

```console
$ doctl k8s cluster create ladybug --region lon1 --size c-2 --count 2

Notice: cluster created, fetching credentials

Notice: adding cluster credentials to kubeconfig file found in "/<CURRENT FOLDER>/.kube/config"

Warning: cluster credentials will expire on <DATETIME>, renew with: "doctl k8s cluster kubeconfig save ladybug"

Notice: cluster is provisioning, waiting for cluster to be running

.............................
ID              Name       Region       Version        Status     Node Pools
<A long UUID>   ladybug    lon1         1.13.1-do.2    running    ladybug-default-pool
```

Now we can use the credentials that are saved locally to interact with our cluster using
`kubectl`. `kubectl` is a command-line tool to deploy and manage applications on
Kubernetes. You can install `kubectl` by following the instructions [here](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl).

Now enter the folder where the config file is created and run `grep name -m 1 config`. 

You will notice the kubectl context will not be called `ladybug` but `do-lon1-ladybug` or
something like `do-<region name>-ladybug`. Switch to this context by running
`kubectl config use-context do-<region name>-ladybug`

```console
$ cd .kube
/.kube$ grep name -m 1 config
  name: do-lon1-ladybug
/.kube$ kubectl config use-context do-lon1-ladybug
Switched to context "do-lon1-ladybug".
```

And check that you can access the cluster by running `kubectl get nodes`

```console
~/.kube$ kubectl get nodes
NAME                   STATUS   ROLES    AGE     VERSION
fervent-poitras-836n   Ready    <none>   4m13s   v1.13.1
fervent-poitras-83a9   Ready    <none>   4m14s   v1.13.1
```

Or you can do it all in one line by using `--kubeconfig` flag:

```console
$ kubectl --kubeconfig=".kube/config" get nodes
NAME                   STATUS   ROLES    AGE     VERSION
fervent-poitras-836n   Ready    <none>   4m13s   v1.13.1
fervent-poitras-83a9   Ready    <none>   4m14s   v1.13.1
```

To destroy the cluster you can use the following command:
```console
$ doctl k8s cluster delete ladybug -v
Warning: Are you sure you want to delete this Kubernetes cluster (y/N) ? y
Notice: cluster deleted, removing credentials

Notice: removing cluster credentials from kubeconfig file found in "<FILEPATH>/.kube/config"

Notice: cluster was set as current context for kubectl. It has been removed, you might want to set a new one.
```
