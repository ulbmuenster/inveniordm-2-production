# Installation and Configuration of Redis

The PostgreSQL installation sets up on the Bitnami helm chart, available on
`https://charts.bitnami.com/bitnami`. As there is just the PVC, we don't have 
a `values.yaml` of our own. Instead we add the needed value directly to the 
command line when deploying the chart.

## Persistence

In order to not loose the cache when Redis is restarted, there should be a volume defined to hold it. This is
done by defining a `PersitentVolumeClaim` in a yml file `cache-claim.yml`:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: cache-claim
  annotations:
    "helm.sh/resource-policy": keep
spec:
  storageClassName: cindergold-delete
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

This is created by calling
```shell
kubectl apply -f cache-claim.yml -n invenio
```

**TODO:** finalize the storage size

## Installation

Finally, the deployment is done by calling
```shell
helm install redis --set architecture=standalone --set master.persistence.existingClaim=cache-claim bitnami/redis
```
