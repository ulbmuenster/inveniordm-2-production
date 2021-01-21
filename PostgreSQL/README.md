# Installation and Configuration of PostgreSQL

The PostgreSQL installation sets up on the Bitnami helm chart, available on 
`https://charts.bitnami.com/charts`. It's configuration is available in the locally available file
`values.yaml`.

## Setting the database, user and password

While it is uncritical to provide database name and user id as clear text in `values.yaml`, providing the 
password isn't and should (and can) be avoided. So we set 
```shell
postgresqlUsername: invenio
postgresqlDatabase: invenio
existingSecret: db-secrets
```
in `values.yaml` and generate a secret `db-secrets`:
```shell
POSTGRESQL_POSTGRES_PASSWORD=$(openssl rand -hex 8)
POSTGRESQL_PASSWORD=$(openssl rand -hex 8)
kubectl create secret generic db-secrets  
  --from-literal="postgresql-postgres-password=$POSTGRESQL_POSTGRES_PASSWORD"
  --from-literal="postgresql-password=$POSTGRESQL_PASSWORD"
```

## Installation

Now with everything prepared we may install PostgreSQL by

```shell
helm repo add bitnami https://charts.helm.sh/stable
helm install postgresql -f ./PostgreSQL/values.yaml bitnami/postgresql -n invenio
```
