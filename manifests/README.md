# Etcd service broker deployment

This folder contains an all-in-one manifest `etcd-cf-service-broker.yml` that will deploy a complete, running etcd cluster, with root credentials enabled, and a service broker that can partition and share the etcd cluster amongst many users.

This deployment is for a relatively modern BOSH director, with Cloud Config enabled. It also uses the latest `bosh2` CLI.

From the root folder:

```
export BOSH_ENVIRONMENT=${BOSH_ENVIRONMENT:?required}
export BOSH_DEPLOYMENT=etcd-cf-service-broker

bosh2 int manifests/etcd-cf-service-broker.yml --var-errs --vars-store=tmp/creds.yml
bosh2 deploy manifests/etcd-cf-service-broker.yml --vars-store=tmp/creds.yml
```

This will fail; but will show you all the required input variables/parameters.

Create a YAML file containing all the required input variables, say `tmp/vars.yml`:

```
...
```

Some variables have been automatically generated into `creds.yml`:

```
servicebroker_password: 7pv25cm9u1seklqjmy8o
```

## Store & retrieve creds from Vault

Instead of generating credentials into a plain text local `tmp/creds.yml`, you might want secrets hidden inside Hashicorp Vault.

Generate the required credentials into Vault using `safe`:

```
export VAULT_PREFIX=secret/etcd-cf-service-broker/demo
safe password $VAULT_PREFIX/servicebroker
```

This will generate random secrets directly into Vault. To sanity check that they are there:

```
safe get $VAULT_PREFIX/servicebroker
```

You can fetch these into an input YAML file via `spruce merge`:

```
spruce merge manifests/spruce-vault-creds.yml
```

This allows you to pass these secrets directly to the `bosh2` command with the `-l` flag:

```
bosh2 int manifests/etcd-cf-service-broker.yml \
  --var-errs \
  -l <(spruce merge manifests/spruce-vault-creds.yml)
bosh2 deploy manifests/dingo-postgresql.yml \
  -l <(spruce merge manifests/spruce-vault-creds.yml)
```
