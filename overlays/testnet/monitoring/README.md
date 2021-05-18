# Monitoring Components

## Instructions for installing monitoring
- kustomize build . > output.yaml
- kubectl get ns monitoring || kubectl create ns monitoring
- kubectl apply -f output.yaml

## Instructions for updating bases. (Should only be required for upgrades)
- `helm template kube-prometheus-stack prometheus-community/kube-prometheus-stack --version 15.4.4 --include-crds --namespace monitoring --values ../../../base/kube-prometheus-stack/kube-prometheus-stack-values.yaml > ../../../base/kube-prometheus-stack/kube-prometheus-stack.yaml`
- ` helm template loki grafana/loki --include-crds --namespace monitoring > ../../../base/loki/loki.yaml`

## Adding Datasources to Grafana
- Create a ConfigMap for the datasource similar to [grafana-datasource-loki](../../../base/kube-prometheus-stack/grafana-datasource-loki.yaml)
- The label 'grafana_datasource: "1"' is required to trigger the grafana-sc-datasource initContainer to add the datasource. Restarting the grafana pod is required for newly added datasources.

TODO:
- Setup Promtail.
- Add Loki and Traefik dashboards to Grafana and provide documentation.
- Setup and use persistent storage.
- Create mainnet overlay.
- Ingress/Endpoints for Grafana.
- Configure alerting.
- Setup Dandelion specific alerting rules.