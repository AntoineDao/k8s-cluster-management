# Digital Ocean setup
We can use the digital ocean api to generate and scale new kubernetes clusters. To do this we will install the digital ocean api client `doctl` by following [these instructions](https://github.com/digitalocean/doctl/blob/master/README.md). As of Jan 2019 we are using version  1.12.2 which has beta support of kubernetes. We enable beta support by running the following command:

> export DIGITALOCEAN_ENABLE_BETA=1

Make sure to have a DO authentication token handy by visitng [this page](https://cloud.digitalocean.com/account/api/tokens) and run the following commands to autenticate your client:

> doctl auth init

Now we can spin up a kubernetes cluster programatically. The example below creates a cluster named `ladybug` with 2 machines of size `c-2` (2 vcpus and 4gb ram each)

> doctl k8s cluster create ladybug --region lon1 --version 1.12.1-do.2 --size c-2 --count 2

Once the clust has been spun up we can save the kubectl credentials to our local kubeconfig by running:

> doctl k8s kubeconfig save ladybug

You will notice the kubectl context will not be called `ladybug` but `do-lon1-ladybug` or something like `do-{region name}-ladybug`. Switch to this context by running:

> kubectl config use-context do-lon1-ladybug

And check that you can access the cluster by running:
> kubectl get nodes
which should return something like:
```console
NAME                   STATUS   ROLES    AGE     VERSION
fervent-poitras-836n   Ready    <none>   4m13s   v1.12.1
fervent-poitras-83a9   Ready    <none>   4m14s   v1.12.1
```