# Kubernetes

Kubernetes folder holds the charts for services installations.

It uses [helm](https://github.com/kubernetes/helm/blob/master/README.md) for
template rendering and deployment management.

## Helm Tool

You can find how to install helm
[here](https://github.com/kubernetes/helm/blob/master/README.md#install) If you
don't afraid to live on the edges you can use the single line installer

```sh
curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get | bash
```

Installation, Upgrades, Downgrades:

Please check how to do the respective operations with:

```sh
helm --help
```

## Charts

### Env

Env chart prepares the environments for the whole system. It creates staging and
production namespaces respectively

```sh
helm upgrade --install rope ./env
```

Notes:

* namespace is not required for this command
* one time command per project

### Grafana

Grafana chart is the "grafana" dashboarding system. Please see the Values.yaml
file for the configuration parameters.

Installation and Upgrades:

```sh
helm upgrade --install graf ./grafana
```

Notes:

* namespace is not required for this command
* one time command per project
* Get the Grafana URL to visit by running these commands in the same shell:

  `export POD_NAME=$(kubectl get pods --namespace default -l
  "app=graf-grafana,component=grafana" -o jsonpath="{.items[0].metadata.name}")`
  `kubectl --namespace default port-forward $POD_NAME 3000`

* The Grafana server can be accessed via port 80 on the following DNS name from
  within your cluster

  `graf-grafana.default.svc.cluster.local`

* Get your 'admin' user password by running:

  `kubectl get secret --namespace default graf-grafana -o
  jsonpath="{.data.grafana-admin-password}" | base64 --decode ; echo`

* Login with the password from previous step and the username: admin

### Keel

Keel chart handles the auto update of the deployments. Please see the
Values.yaml file for the configuration parameters.

Installation and Upgrades:

```sh
helm upgrade --install --namespace <namespace> keel ./keel
```

Notes:

* namespace should be a system namespace
* one time command per project

### Nginx Ingress

Nginx Ingress chart handles the auto routing/discovery for the backend services.
Given the services and Ingress pairs, this ingress controller creates _a_ load
balancer and routes all the traffice from that endpoint into the internal
ingresses. Please see the Values.yaml file for the configuration parameters.

Installation and Upgrades:

```sh
helm upgrade --install --namespace nginx-ingress nginx-ingress ./nginx-ingress
```

Notes:

* namespace should be nginx-ingress namespace
* one time command per project

### Prometheus

Prometheus is the cloud native monitoring and metrics system where most the
kubernetes related projects has built-in support for it. Please see the
Values.yaml file for the configuration parameters.

Installation and Upgrades:

```sh
helm upgrade --install prom ./prometheus
```

Notes:

* one time command per project

### Docker GC

Spotify docker GC chart removes the unused images from the system, basically
handles the GC. Please see the Values.yaml file for the configuration
parameters.

Installation and Upgrades:

```sh
helm upgrade --install docker-gc ./spotify-docker-gc
```

Notes:

* one time command per project

### Count

Count chart deploys both "counter" and "compactor" workers. Please see the
Values.yaml file for the configuration parameters.

Installation and Upgrades:

```sh
helm upgrade --install --namespace <namespace> counter ./count
helm upgrade --install --namespace <namespace> compactor ./count
```

Notes:

* namespace is _required_ for this command
* required per separate environments

### Home

Home chart deploys [home](github.com/ropelive/home). Please see the Values.yaml
file for the configuration parameters.

Installation and Upgrades:

```sh
helm upgrade --install --namespace <namespace> home ./home
```

Notes:

* namespace is _required_ for this command
* required per separate environments

### Rest

Rest chart deploys [rest](github.com/ropelive/rest). Please see the Values.yaml
file for the configuration parameters.

Installation and Upgrades:

```sh
helm upgrade --install --namespace <namespace> rest ./rest
```

Notes:

* namespace is _required_ for this command
* required per separate environments

### Routing

Routing chart deploys the ingress configuration for rope system. Please see the
Values.yaml file for the configuration parameters.

Installation and Upgrades:

```sh
helm upgrade --install --namespace <namespace> routing ./routing
```

Notes:

* namespace is _required_ for this command
* required per separate environments

### Server

server chart deploys [server](github.com/ropelive/server). Please see the
Values.yaml file for the configuration parameters.

Installation and Upgrades:

```sh
helm upgrade --install --namespace <namespace> server ./server
```

Notes:

* namespace is _required_ for this command
* required per separate environments

### Twine

Twine chart deploys [twine](github.com/ropelive/twine). Please see the
Values.yaml file for the configuration parameters.

Installation and Upgrades:

```sh
helm upgrade --install --namespace <namespace> twine ./twine
```

Notes:

* namespace is _required_ for this command
* required per separate environments

### MongoDB

mongodb chart deploys [mongodb](https://www.mongodb.com/). Please see the
Values.yaml file for the configuration parameters.

Installation and Upgrades:

note: Do not forget to add namespace as a prefix, mongodb uses PVC and PVCs are
not namepsaced.

```sh
helm upgrade --install --namespace <namespace> <namespace>-mongodb ./mongodb
```

After deploying you can connect to your cluster with the following command,

```sh
kubectl run --namespace <namespace> mongodb-mongodb-client --rm --tty -i --image bitnami/mongodb --command -- mongo --host mongodb-mongodb -p minda
# kubectl run {{ template "mongodb.fullname" . }}-client --rm --tty -i --image bitnami/mongodb --command -- mongo --host {{ template "mongodb.fullname" . }} {{- if .Values.mongodbRootPassword }} -p {{ .Values.mongodbRootPassword }}
```

After connecting to your mongo, you can create a user with the following
command.

```sh
use <your db name>
db.createUser(
   {
     user: "rope_admin",
     pwd: "minda",
     roles: [ "readWrite", "dbAdmin" ]
   }
)
```

Notes:

* namespace is _required_ for this command
* required per separate environments

### Redis

redis chart installs a redis server.

Installation and Upgrades:

note: Do not forget to add namespace as a prefix, mongodb uses PVC and PVCs are
not namepsaced.

```sh
helm upgrade --install --namespace <namespace> <namespace>-redis ./redis
```

```sh
kubectl run --namespace <namespace> name-redis-client --rm --tty -i --image bitnami/redis:3.2.9-r2 -- bash
```

Notes:

* namespace is _required_ for this command
* required per separate environments

## Chart Version Upgrades

Please note that the Values.yaml files might have been changed to satisfy our
needs. Merge the changes according to new requirements and push accordingly.

```sh
# last update to all of them at 2017-11-17
helm fetch stable/grafana --untar --destination ./kubernetes
helm fetch stable/keel --untar --destination ./kubernetes
helm fetch stable/mongodb --untar --destination ./kubernetes
helm fetch stable/nginx-ingress --untar --destination ./kubernetes
helm fetch stable/prometheus --untar --destination ./kubernetes
helm fetch stable/redis --untar --destination ./kubernetes
helm fetch stable/spotify-docker-gc --untar --destination ./kubernetes
helm fetch stable/kube-lego --untar --destination ./kubernetes/kube-lego
```

## Steps for deployment

### Multi env installations

Please see helm.sh for further info.

### Single installations per deployment

Please see helm.sh for further info.
