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
to upgrade to the latest available version of `invenio-cli`. Then use it to scaffold
the new version of InvenioRDM:
```shell
invenio-cli init rdm
```
Make sure to choose the same Python version as installed in the VM (Python 3.6).
There are some configuration files to be edited to alter behaviour and look&feel 
of InvenioRDM. To change the maximal upload size edit the file
`docker/nginx/conf.d/default.conf`. In the section
```shell
location ~ /api/records/.+/draft/files/.+/content
```
change `max_client_body_size` to the size you want.

Main configuration file is `invenio.cfg`.
Next call
```shell
invenio-cli install --pre
```
and build the docker image with
```shell
docker build -t ulb.wwu.io/wwurdm/invenio:n.0.0 . --build-arg include_assets=true
docker push ulb.wwu.io/wwurdm/invenio:n.0.0
```

