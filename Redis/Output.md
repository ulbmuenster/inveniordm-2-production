Redis can be accessed via port 6379 on the following DNS names from within your cluster:

redis-master.invenio.svc.cluster.local for read/write operations
redis-slave.invenio.svc.cluster.local for read-only operations


To get your password run:

    export REDIS_PASSWORD=$(kubectl get secret --namespace invenio redis -o jsonpath="{.data.redis-password}" | base64 --decode)

To connect to your Redis(TM) server:

1. Run a Redis(TM) pod that you can use as a client:
   kubectl run --namespace invenio redis-client --rm --tty -i --restart='Never' \
   --env REDIS_PASSWORD=$REDIS_PASSWORD \
   --image docker.io/bitnami/redis:6.0.10-debian-10-r1 -- bash

2. Connect using the Redis(TM) CLI:
   redis-cli -h redis-master -a $REDIS_PASSWORD
   redis-cli -h redis-slave -a $REDIS_PASSWORD

To connect to your database from outside the cluster execute the following commands:

    kubectl port-forward --namespace invenio svc/redis-master 6379:6379 &
    redis-cli -h 127.0.0.1 -p 6379 -a $REDIS_PASSWORD
