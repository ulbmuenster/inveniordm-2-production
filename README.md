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

Right now the only available documentation on how to setup is available for 
[PostgreSQL](./PostgreSQL/README.md).

## Build the InvenioRDM image