# Installation and Configuration of RabbitMQ

## Setting the user and password

InvenioRDM is configured with the user `guest` and comes along with a secret `mq-secrets` 
containing the needed information to configure RabbitMQ and InvenioRDM accordingly:

```shell
$ RABBITMQ_DEFAULT_PASS=$(openssl rand -hex 8)
$ kubectl create secret generic \
  --from-literal="RABBITMQ_DEFAULT_PASS=$RABBITMQ_DEFAULT_PASS" \
  --from-literal="rabbitmq-password=$RABBITMQ_DEFAULT_PASS" \
  --from-literal="CELERY_BROKER_URL=amqp://guest:$RABBITMQ_DEFAULT_PASS@rabbitmq:5672/" \
  mq-secrets --namespace invenio
secret "mq-secrets" created
```

In `values.yaml` the following entries must be changed/set:
```yaml
auth:
  username: guest
  existingPasswordSecret: mq-secrets
```

The helm chart then takes the RabbitMQ from the key `rabbitmq-password`.

## Plugins

As the Bitnami RabbitMQ image comes already with RabbitMQ management enabled, there is nothing
to add.

## Persistence

Persistence is automatically enabled in the RabbitMQ helm chart. We have only to set the storageClass
by adding
```yaml
persistence:
  storageClass: cindergold
```
in `values.yaml`.

## Installation

Finally we call 

```shell
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install rabbitmq -f ./RabbitMQ/values.yaml bitnami/rabbitmq -n invenio
```