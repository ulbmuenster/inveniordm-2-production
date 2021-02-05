# Installation and Configuration of RabbitMQ

## Plugins

To support AMQP 1.0 we add the appropriate PlugIn by adding it's name in `values.yaml`:

```yaml
plugins: 'rabbitmq_management rabbitmq_peer_discovery_k8s rabbitmq_amqp1_0'
```

## Persistence

Persistence is automatically enabled in the RabbitMQ helm chart. We have only to set the storageClass
by adding
```yaml
persistence:
  storageClass: cindergold-delete
```
in `values.yaml`.

## Installation

Finally we call 

```shell
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install rabbitmq -f ./RabbitMQ/values.yaml bitnami/rabbitmq -n invenio
```