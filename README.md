# Deploy InvenioRDM to Production

## Disclaimer

This repository contains configuration files and documentation about getting InvenioRDM  productive. 
This includes `values.yaml` files for helm based database setup, messaging setup, etc. and documentation
of the decisions made.

Every component is documented in it's own subdirectory.

## System components

The deployment of InvenioRDM consists of several products:
- PostgreSQL
- RabbitMQ
- ElasticSearch
- Redis

The documentations on how to setup the components are available for 
[PostgreSQL](./PostgreSQL/README.md), [RabbitMQ](./RabbitMQ/README.md), 
[ElasticSearch](./ElasticSearch/README.md) and [Redis](./Redis/README.md).

## Build the InvenioRDM image

For this task I've created a VirtualBox image from the latest CentOS 7 with the 
dependencies intalled. Don't forget to activate the network connection after startup
of the VM.
With `pip` installed call
```shell
sudo pip install -U invenio-cli
```
to upgrade to the latest available version of `invenio-cli`. Before using it to scaffold
the new version of InvenioRDM make sure you are using the correct version of node/npm! Then call
```shell
invenio-cli init rdm -c vx.y
```
Make sure to choose the correct Python version available (actually 3.9).
The InvenioRDM instance is mainly configured in the `values.yaml` of the InvenioRDM helm chart. However, make sure 
the main configuration file `invenio.cfg` contains the section
```shell
SQLALCHEMY_ENGINE_OPTIONS = {
    'pool_pre_ping': True,
    'max_overflow': 10,
    'pool_size': 5,
    'pool_recycle': 3600,
    'pool_timeout': 30,	
}
```
Next call
```shell
invenio-cli install --pre
```
and build the docker image with
```shell
docker build -t ulb.wwu.io/wwurdm/invenio:x.y.0 . --build-arg include_assets=true
docker push ulb.wwu.io/wwurdm/invenio:x.y.0
```

## Deploy InvenioRDM

First, creat a certificate, run
```shell
kubectl apply -f certificate.yml -n invenio
```
