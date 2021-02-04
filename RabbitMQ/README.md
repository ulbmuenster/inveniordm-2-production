# Installation and Configuration of RabbitMQ

## Installation

Finally we call 

```shell
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install rabbitmq -f ./RabbitMQ/values.yaml bitnami/rabbitmq -n invenio
```