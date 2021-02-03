# Installation and Configuration of PostgreSQL

The PostgreSQL installation sets up on the Bitnami helm chart, available on 
`https://charts.bitnami.com/bitnami`. It's configuration is available in the locally available file
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
$ POSTGRESQL_POSTGRES_PASSWORD=$(openssl rand -hex 8)
$ POSTGRESQL_PASSWORD=$(openssl rand -hex 8)
$ POSTGRESQL_USER=invenio
$ POSTGRESQL_HOST=postgresql
$ POSTGRESQL_PORT=5432
$ POSTGRESQL_DATABASE=invenio
$ POSTGRESQL_REPLICATION_PASSWORD=$(openssl rand -hex 8)
$ kubectl create secret generic db-secrets  
  --from-literal="postgresql-postgres-password=$POSTGRESQL_POSTGRES_PASSWORD"
  --from-literal="postgresql-password=$POSTGRESQL_PASSWORD"
  --from-literal="postgresql-replication-password=$POSTGRESQL_REPLICATION_PASSWORD"
  --from-literal="POSTGRESQL_PASSWORD=$POSTGRESQL_PASSWORD"
  --from-literal="SQLALCHEMY_DB_URI=postgresql+psycopg2://$POSTGRESQL_USER:$POSTGRESQL_PASSWORD@$POSTGRESQL_HOST:$POSTGRESQL_PORT/$POSTGRESQL_DATABASE"
  -n invenio
```

## Persistence

In order to not loose any data when PostgreSQL is restarted, there should be a volume defined to hold it. This is
done by defining a `PersitentVolumeClaim` in a yml file `db-claim.yml`:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: db-claim
  annotations:
    "helm.sh/resource-policy": keep
spec:
  storageClassName: cindergold-delete
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```
**TODO:** finalize the storage size

Then set in `values.yaml`
```yaml
persistence:
  enabled: true
  existingClaim: db-claim
```
and call
```shell
kubectl apply -f db-claim.yml -n invenio
```

## Replication

Having the replication password allready set in `db-secrets` we define the replica specification in `values.yml`
as follows:
```yaml
replication:
  enabled: true
  user: invenio_repl
  password: repl_password
  readReplicas: 2
  synchronousCommit: 'on'
  numSynchronousReplicas: 2
  applicationName: invenio_rdm
```

The password was already set as part of `db-secrets`. The number of replicas is set to `2` and they are 
configured as synchronous replicas, so every transaction waits until the logs are commited on the replicas.

A caveat is that there is no possibility to define PersitentVolume's to hold the data of the read replicas.

**Attention**: Everything defined here is executed at database initialization time. If you first try without 
replication this will not work, as there is no user `invenio_repl` defined in the database! You'll first have to remove
the PersistentVolumeClaim `db-claim`, so everything is reinitialized.

## Backup

One possibility in PostgreSQL to backup the data is using the command `pg_dump`, but this doesn't allow 
incremental backup. To implement continuous-archiving it is possible to archive the write-ahead log (WAL).
As replication is already activated (so streaming of the logs is active) we only have to set `archive_mode=on`
and define an archive command that does the trick. So in `values.yaml` we add:
```yaml
postgresqlExtendedConf: { "archiveMode": "on", "archiveCommand": "\'/bin/true\'" }
```

**TODO:** define a useful archive command and perhaps a restore command?!

## Installation

Now with everything prepared we may install PostgreSQL by

```shell
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install postgresql -f ./PostgreSQL/values.yaml bitnami/postgresql -n invenio
```
