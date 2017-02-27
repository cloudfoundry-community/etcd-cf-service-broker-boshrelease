# etcd-cf-service-broker release for BOSH

## Usage

This section is a tutorial for deploying this release, including a sample single-node etcd cluster (using [etcd-release](https://github.com/cloudfoundry-incubator/etcd-release)), to a [bosh-lite](https://bosh.io/docs/bosh-lite.html).

Create a small manifest with passwords for the service broker (`meta.broker.password`), and for the etcd `root` user (`meta.etcd.root_password`).

The etcd cluster itself will be deployed without authentication, and the broker will enable etcd authentication and create the `root` user that it will subsequently use to create/delete etcd roles and users.

```
---
meta:
  broker:
    password: "iaCbzhFzmuwChfPUr2Yg"
  etcd:
    root_password: "hPMvYHckHMWKnATCWssHPbwp8ub"
```

If this file is saved as `tmp/hub-lite.yml`, then to generate the BOSH deployment manifest:

```
./templates/make_manifest warden templates/releases.yml tmp/hub-lite.yml
```

The `templates/releases.yml` will include links to the latest final releases for `etcd-cf-service-broker` and `etcd`, and your BOSH will download them if missing.

To start the deployment of your new etcd/broker cluster:

```
bosh deploy
```

Finally, to confirm that your system is working you can run the `sanity-test` errand:

```
bosh run errand sanity-test
```

You can also manually interact with your broker (from inside your bosh-lite so you can access the `10.244.37.2` container):

```
curl -u broker:password http://10.244.37.2:6000/v2/catalog
```

### Public etcd URL

If your etcd cluster has a public URL that is different from your internal etcd access, you can configure the broker to override the URL in binding credentials.

Pass `meta.broker.public_etcd_url` within your YAML file and re-run the `./templates/make_manifest` command again to re-generate your manifest; then deploy again.

```
---
meta:
  etcd:
    root_password: "^hPMvYHckHMWKnATCWssHPbwp8ub"
  broker:
    password: broker
    public_etcd_url: http://10.58.111.45:4001
```

```
./templates/make_manifest warden templates/releases.yml tmp/hub-lite.yml
bosh deploy
```
