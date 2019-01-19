# Database Setup
To create a Postgres DB on kubernetes we need to configure a volume for data to be stored, create users and secure their authentication in a secret, and expose the pod through the right container to the rest of the cluster.

1. Volume: Spin up a DigitalOcean volume (the actual disk where data is hosted) using the `PersistentVolumeClaim` objec type in the `volume.yml` file. We also add an `init-container` to create a special directory in the volume for the postgres data.
2. Admin Password: Configure the admin password as a secret to be loaded in the postgres image as an environment variable. Be sure to save it as a byte 64 type by running the following command in terminal:
> echo -n "supersecretpassword" | base64
3. Service Deployment: Create a simple service exposing post `5432` to the rest of the cluster

# Acess
## PSQL
You can access the database directly through `psql` on the container by running the following command:
> kubectl exec -it pg-database psql -- postgres -U postgres -W

And enter the password (`supersecretpassword`) when prompted.

## Port-Forwarding
Alternatively you can port-forward the `db-service` to your local machine and access the database through a pgAdmin client for example. To do so run the following command:
```
kubectl port-forward svc/db-service 5432:8889
```

You can test this works by running the command below and entering the correct password when prompted:

```
psql -h localhost -p 8889 -U postgres -W 
```