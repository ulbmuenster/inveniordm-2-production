# Installation and Configuration of ElasticSearch 7.10.1

This is only temporary. We will go for OpenSearch when it's clear it is supported.

## Setting the host

As we are going to use a self-installed Elasticsearch we have to generate a secret to tell 
InvenioRDM how to get hold of it:

``` console
$ export INVENIO_SEARCH_ELASTIC_HOSTS="[{'host': 'elasticsearch-master', 'timeout': 30, 'port': 9200}]"
$ kubectl create secret generic \
  --from-literal="INVENIO_SEARCH_ELASTIC_HOSTS=$INVENIO_SEARCH_ELASTIC_HOSTS" \
  elasticsearch-secrets --namespace invenio
```

## Installation

We may install ElasticSearch by

```shell
helm repo add elastic https://helm.elastic.co
helm install elasticsearch -f ./ElasticSearch/values.yaml elastic/elasticsearch -n invenio
```
