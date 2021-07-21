# Installation and Configuration of ElasticSearch 7.10.1

## Installation

We may install ElasticSearch by

```shell
helm repo add elastic https://helm.elastic.co
helm install elasticsearch -f ./ElasticSearch/values.yaml elastic/elasticsearch -n invenio
```
