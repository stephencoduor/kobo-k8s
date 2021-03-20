# kobo

This is a helm Chart for [KoboToolbox](https://www.kobotoolbox.org/).

## How to run

### Prerequisites

* A Kubernetes cluster
* [Helm](https://helm.sh/)

### Limitations

* The built-in nginx container only supports http for now. It is expected that https support will typically be handled at the ingress level
* This setup requires the availability of 3 subdomains under the same root domain (just like `kobo-install`)
* Due to a [known issue](https://community.kobotoolbox.org/t/login-not-verified-500-internal-server-error/11360/5), the `kpi` container sometimes tries to access `kobocat` via its *public* address. This means that your DNS and possibly TLS handling must be in place for the full setup to work - setting those up in your local `hosts` file will not suffice. The symptom for this problem is that logging in to the main Kobotoolbox UI will appear to succeed, but most features won't work. Calls to `/me` will return an HTTP 500 error. A workaround is provided that lets you force an appropriate DNS resolution in the `kpi` container, but this will only work if your external address is configured as `http`, not `https`.

### Setup

Refer to the [`values.yaml`](helm/kobotoolbox/values.yaml) for details of all the variables that can be overridden, and create your own overrides in a separate file, e.g. `my-values.yaml`.

In particular you will need to setup a number of secrets, as well as provide a valid public domain name that the application will be reachable on.

Then, install the helm release as usual.

```sh
# Clone the project
git clone https://github.com/one-acre-fund/kobo-k8s && cd kobo-k8s

# Install chart dependencies
helm dependency update helm/kobotoolbox

# Override desired values in your own override file
cat <<EOF > my-values.yaml
postgresql:
  postgresqlPostgresPassword: some_password
EOF

# Install chart
helm install --values my-values.yaml kobo helm/kobotoolbox
```

You should see a bunch of new pods popping up:

```sh
$ kubectl get pods
oaf-kobo-kobo-694bb8449d-55gjl                                 4/4     Running     0          13m
oaf-kobo-mongodb-5fc744955b-z76j9                              1/1     Running     0          125m
oaf-kobo-postgresql-0                                          1/1     Running     0          125m
oaf-kobo-rediscache-master-0                                   1/1     Running     0          125m
oaf-kobo-redismain-master-0                                    1/1     Running     0          125m
```

## References

* [Kobo Toolbox](https://www.kobotoolbox.org/)
* [kobo-docker](https://github.com/kobotoolbox/kobo-docker)

## TODO

* Publish to [ArtifactHub](https://artifacthub.io/)
* Auto-generate doc from values.yaml with `helm-docs`
* Add `test` hooks
