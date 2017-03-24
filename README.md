# etcd-cf-service-broker release for BOSH

## Usage

Once this broker has been deployed, it can either be used indirectly (via Cloud Foundry for example) or directly (via its API).

The Cloud Foundry user's experience might be:

```
cf create-service etcd shared my-etcd
cf bind-service myapp my-etcd
cf restart myapp
```

Above is the typical use case - provision a service instance and bind it to an application.

If you want access to the etcd cluster yourself, Cloud Foundry CLI has the `cf service-key` commands:

```
cf create-service-key my-etcd my-etcd-drnic
cf service-key my-etcd my-etcd-drnic
```

The access credentials will be displayed to the terminal.

**Note:** users/applications will be accessing a shared etcd cluster and must scope all their `/v2/keys` get/set requests within the provided path. Requests made outside this path will return etcd auth errors.

Similarly, users/applications must access etcd using the basic auth credentials provided to access their `/v2/keys` subpath.

## Deployment

This BOSH release includes a very easy to use [`manifests/etcd-cf-service-broker.yml`](manifests/etcd-cf-service-broker.yml) manifest.

See [`manifests/README.md`](manifests/README.md) for extra instructions.
