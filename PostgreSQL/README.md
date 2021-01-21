# Installation and Configuration of PostgreSQL

The PostgreSQL installation sets up on the Bitnami helm chart, available on 
`https://charts.bitnami.com/charts`. It's configuration is available in the locally available file
`values.yaml`.

## Setting the password

## Setting the database and user

## Security

## Installation

Now with everything prepared we may install PostgreSQL by

```shell
helm repo add bitnami https://charts.helm.sh/stable
helm install postgresql -f ./PostgreSQL/values.yaml bitnami/postgresql -n invenio
```
