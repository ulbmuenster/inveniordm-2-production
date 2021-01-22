```shell
NAME: postgresql
LAST DEPLOYED: Fri Jan 22 13:22:44 2021
NAMESPACE: invenio
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
** Please be patient while the chart is being deployed **

PostgreSQL can be accessed via port 5432 on the following DNS name from within your cluster:

    postgresql.invenio.svc.cluster.local - Read/Write connection

To get the password for "postgres" run:

    export POSTGRES_ADMIN_PASSWORD=$(kubectl get secret --namespace invenio db-secrets -o jsonpath="{.data.postgresql-postgres-password}" | base64 --decode)

To get the password for "invenio" run:

    export POSTGRES_PASSWORD=$(kubectl get secret --namespace invenio db-secrets -o jsonpath="{.data.postgresql-password}" | base64 --decode)

To connect to your database run the following command:

    kubectl run postgresql-client --rm --tty -i --restart='Never' --namespace invenio --image docker.io/bitnami/postgresql:13.1.0-debian-10-r66 --env="PGPASSWORD=$POSTGRES_PASSWORD" --command -- psql --host postgresql -U invenio -d invenio -p 5432



To connect to your database from outside the cluster execute the following commands:

    kubectl port-forward --namespace invenio svc/postgresql 5432:5432 &
    PGPASSWORD="$POSTGRES_PASSWORD" psql --host 127.0.0.1 -U invenio -d invenio -p 5432
```