# istio
Istio is a super useful microservice management platform which integrates nicely with
kubernetes. Here is a link to their website: https://istio.io. Istio requires a slightly
beefier machine than the standard mini setup (mainly higher RAM requirement) so be sure
to pick a cluster with a machine of at least 4GB RAM.

## Initial Setup
### Download (latest):
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


### Istio install

Apply CRDs:

```shell
$ kubectl apply -f ~/istio-1.0.5/install/kubernetes/helm/istio/templates/crds.yaml
```

Wait a few seconds.

Now implement Istio with mutual TLS authentication.

```shell
$ kubectl apply -f ~/istio-1.0.5/install/kubernetes/istio-demo-auth.yaml
```

Setup automated side car injection in `default` namespace:

```shell
$ kubectl label namespace default istio-injection=enabled
```

## Service Mesh Configuation
The service mesh can be configured to manage authentication, tls encryption, and traffic routing. The Istio documentation divides the main aspects of mesh configuration into 4 categories. For this project each category is represented by a folder which contains configuration and documentation to help setup the service mesh:
1. [Traffic Management](/istio/traffic-management)
2. [Security](/istio/security) - Partially implemented
3. [Policies](/istio/policies) - Not implemented yet
4. [Telemetry](/istio/telemetry) - Partially implemented