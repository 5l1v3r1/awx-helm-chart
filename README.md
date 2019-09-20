# Helm chart for AWX

Installs AWX 7.0.0

The [AWX project](https://github.com/ansible/awx) doesn't support Helm as an official install method. This chart is based on their Ansible playbook install method for Kubernetes.

RabbitMQ and Memcached are deployed in the StatefulSet. An external PostgreSQL database (9.6+) needs to be provided.

The database migration is done through a Helm pre-install and pre-upgrade hook. Unfortunately, required resources for the migration also need to be hooks. These resources can't be automatically deleted after the database migration since the StatefulSet needs them. This isn't normally a problem for installs and upgrades but these resources won't be deleted along with a release deletion.

## Installing the Chart

Provide an external PostgreSQL database in `values.yaml`:

```yaml
postgres:
  hostname: postgres
  username: awx
  password: awxpass
  database: awx
  port: 5432
```

Then install with the release name `my-release`:

```console
$ helm install . --name my-release
```

## Uninstalling the Chart

To uninstall the `my-release` deployment:

```console
$ helm delete my-release
```

The command deletes the release and removes the Kubernetes components associated with the chart except for these hook resources:
* configMap `my-release`-config
* configMap `my-release`-application-credentials
* secret `my-release`-secrets
