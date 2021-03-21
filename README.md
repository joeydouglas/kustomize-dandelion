# Requirements

* Kubernetes. For local k8s development environment, we encourage you to use [k3s]/[k3d] lightweight k8s distro.
* [kubectl]
* [Kustomize]

# Local deployment

## Setup local k3d 

Assuming you are already running [docker] locally, and have both, [kubectl] and [k3d] binaries installed, you could create a `k3d` cluster like this (customize port mappings if you feel to):
```
CLUSTER_NAME=dandelion-testnet
k3d cluster create \
  -p "80:80@loadbalancer" \
  -p "443:443@loadbalancer" \
  ${CLUSTER_NAME}
kubectl config use-context k3d-${CLUSTER_NAME}
kubectl get pods -A
```

## Render k8s manifests

``` 
OVERLAY=testnet
OUTPUT_FILE=overlays/${OVERLAY}/output.yaml # this will contain the whole deploy manifest
kustomize build \
  --enable-alpha-plugins \
  overlays/${OVERLAY} > overlays/${OVERLAY}/output.yaml
```

## Deploy k8s manifests

* Using plain `kubectl`:
```
NAMESPACE=dandelion-testnet
OVERLAY=testnet
kubectl get ns ${NAMESPACE} || kubectl create ns ${NAMESPACE}
kubectl apply -n ${NAMESPACE} -f overlays/${OVERLAY}/output.yaml
```
* Using convenient [kapp] tool to get diff between local manifests and currently deployed ones in cluster:
```
OVERLAY=testnet
NAMESPACE=dandelion-${OVERLAY}
APP_NAME=${NAMESPACE}
kubectl get ns ${NAMESPACE} || kubectl create ns ${NAMESPACE}
kapp deploy -c \
  --app ${APP_NAME} \
  --namespace ${NAMESPACE} \
  --file overlays/${OVERLAY}/output.yaml
```

# Usage

Given that everything is listening on `localhost` we need to specify `Host` headers for the requests to be properly routed by the `Ingress` (on k3d, [traefik] by default).
If you prefer, you could fake these hostnames to point to `127.0.0.1` on your `/etc/hosts` (linux/darwin) file so you don't need to specify them for each request.

* Get available endpoints:
```
NAMESPACE=dandelion-testnet
kubectl get ingress -n ${NAMESPACE}
```
* Check postgres[t] db tables
```
ENDPOINT=postgrest-api.testnet.local
curl -kL -H "Host: ${ENDPOINT}" \
  https://localhost
```
* Check sync progress
```
ENDPOINT=graphql-api.testnet.local
curl -kL \
     -H "Host: ${ENDPOINT}" \
     -H 'Accept-Encoding: gzip, deflate, br' \
     -H 'Content-Type: application/json' \
     -H 'Accept: application/json' \
      --data-binary '{"query":"query cardanoDbSyncProgress {\n    cardanoDbMeta {\n        initialized\n        syncPercentage\n    }\n}\n"}'
     'https://localhost/'
```

# Operations

* Start from the scratch:
```
NAMESPACE=dandelion-testnet
kubectl delete ns ${NAMESPACE}
```
* Just [deploy again :)](#deploy-k8s-manifests)

[docker]: https://docs.docker.com/engine/install/
[kustomize]: https://kustomize.io
[kubectl]: https://kubernetes.io/es/docs/tasks/tools/install-kubectl
[k3d]: https://k3d.io
[k3s]: https://k3s.io
[kapp]: https://github.com/vmware-tanzu/carvel-kapp
[traefik]: https://traefik.io/traefik/
