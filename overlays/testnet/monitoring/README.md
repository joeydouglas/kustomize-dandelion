# Monitoring Components

## Instructions for installing monitoring
- kustomize
- kubectl apply

## Instructions for updating bases
- `helm template kube-prometheus-stack prometheus-community/kube-prometheus-stack --version 15.4.4 --include-crds --values ../../../base/kube-prometheus-stack/kube-prometheus-stack-values.yaml > ../../../base/kube-prometheus-stack/kube-prometheus-stack.yaml`
- ` helm template loki grafana/loki --include-crds  > ../../base/kube-prometheus-stack/loki.yaml`

## Adding Datasources to Grafana
- Working on this now.

TODO:
- Add dashboards to Grafana and provide documentation.
- Setup and use persistent storage.
- Create mainnet overlay.
- Ingress/Endpoints for Grafana.