# Installation and Configuration of PostgreSQL

The PostgreSQL installation sets up on the Bitnami helm chart, available on 
`https://charts.bitnami.com/charts`. It's configuration is available in the locally available file
`values.yaml`.

## Setting the database, user and password

While it is uncritical to provide database name and user id as clear text in `values.yaml`, providing the 
password isn't and should (and can) be avoided. So we set 
```yaml
postgresqlUsername: invenio
postgresqlDatabase: invenio
existingSecret: db-secrets
```
in `values.yaml`. Now me just might to generate the secret `db-secrets`, but we might as well add the information
which is later needed to configure InvenioRDM (the content of `POSTGRESQL_HOST` is defined as the name of the 
Kubernetes service):
```shell
POSTGRESQL_POSTGRES_PASSWORD=$(openssl rand -hex 8)
POSTGRESQL_PASSWORD=$(openssl rand -hex 8)
POSTGRESQL_USER=invenio
POSTGRESQL_HOST=postgresql
POSTGRESQL_PORT=5432
POSTGRESQL_DATABASE=invenio
kubectl create secret generic db-secrets  
  --from-literal="postgresql-postgres-password=$POSTGRESQL_POSTGRES_PASSWORD"
  --from-literal="postgresql-password=$POSTGRESQL_PASSWORD"
  --from-literal="POSTGRESQL_PASSWORD=$POSTGRESQL_PASSWORD"
  --from-literal="SQLALCHEMY_DB_URI=postgresql+psycopg2://$POSTGRESQL_USER:$POSTGRESQL_PASSWORD@$POSTGRESQL_HOST:$POSTGRESQL_PORT/$POSTGRESQL_DATABASE"
```

**TODO:** Replication

## Installation

Now with everything prepared we may install PostgreSQL by

```shell
helm repo add bitnami https://charts.helm.sh/stable
helm install postgresql -f ./PostgreSQL/values.yaml bitnami/postgresql -n invenio
```
