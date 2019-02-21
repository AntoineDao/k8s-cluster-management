# Cert-Manager
Not using `https` for any web application in this day and age is not acceptable, therefore we need to ensure a mechanism is in place to generate SSL certificates and add them to the cluster ingress. 

## 1. Deploy Cert-Manager
Deploying cert manager to kubernetes can be done following the commands below:

```console
<!-- kubectl create namespace cert-manager -->
kubectl label namespace istio-system certmanager.k8s.io/disable-validation=true
kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.6/deploy/manifests/00-crds.yaml
kubectl apply -f cert-manager.yml
```

For more info on this process check out the [`cert-manager` docs](https://docs.cert-manager.io/en/release-0.6/getting-started/install.html).
Note: we are using v0.6 of `cert-manager` and have attached the yaml file to this folder for documentation purposes.

## 2. Add DigitalOcean API Token
In order to work it's magic validating domain names against servers `cert-manager` needs access to the DNS provider. This can be done through DigitalOcean by passing it the DO-API token:

```console
kubectl create secret generic digitalocean-dns -n=cert-manager --from-file=access-token
```

## 3. Create Certificate Issuer
The certificate issuer is the pod that will interact with the DigitalOcean DNS provider to request certificates from LetsEncrypt.

```console
kubectl apply -f issuer.yml
```

Ensure it is properly configured by running:
```console
kubectl describe clusterissuer/letsencrypt-issuer
```

Which should return something like this:
```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
spec:
  acme:
    dns01:
      providers:
      - cnameStrategy: ""
        digitalocean:
          tokenSecretRef:
            key: access-token
            name: digitalocean-dns
        name: cloud-dns-provider
    email: admin@pollination.solutions
    privateKeySecretRef:
      key: ""
      name: letsencrypt-issuer
    server: https://acme-v02.api.letsencrypt.org/directory
status:
  acme:
    uri: https://acme-v02.api.letsencrypt.org/acme/acct/51666573
  conditions:
  - lastTransitionTime: "2019-02-17T12:05:48Z"
    message: The ACME account was registered with the ACME server
    reason: ACMEAccountRegistered
    status: "True"
    type: Ready
```

## 4. Get Certified!
Now that we have a certificate issuer in place we can request new certificates. This is done by running the command below:
```console
kubectl apply -f certificate.yml
```

Check the status of this certificate by running:
```console
kubectl get certificate --namespace istio-system api-gateway-certificate -o yaml
```
Which should return something like this:
```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  annotations:
spec:
  acme:
    config:
    - dns01:
        provider: cloud-dns-provider
      domains:
      - '*.pollination.cloud'
  commonName: '*.pollination.cloud'
  issuerRef:
    kind: ClusterIssuer
    name: letsencrypt-issuer
  secretName: istio-ingressgateway-certs
status:
  conditions:
  - lastTransitionTime: "2019-02-21T18:17:17Z"
    message: Certificate is up to date and has not expired
    reason: Ready
    status: "True"
    type: Ready
  notAfter: "2019-05-22T17:17:16Z"
```