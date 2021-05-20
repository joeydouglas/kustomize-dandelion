# Monitoring Components (kube-prometheus-stack and loki-stack)

## Instructions for installing monitoring
- kustomize build . > output.yaml
- kubectl get ns monitoring || kubectl create ns monitoring
- kubectl apply -f output.yaml

## Adding Datasources to Grafana
- Create a ConfigMap for the datasource similar to [grafana-datasource-loki](../../../base/kube-prometheus-stack/grafana-datasource-loki.yaml).
- The label 'grafana_datasource: "1"' is required to trigger the grafana-sc-datasource initContainer to add the datasource. Restarting the grafana pod is required for newly added datasources.

## Adding Dashboards to Grafana
- Create a ConfigMap for the dashboard similar to [traefik-dashboard](../../../base/kube-prometheus-stac/traefik-dashboard.yaml).
- Grafana should load the dashboard automatically without restarting the pod.

## Instructions for updating bases. (Should only be required for upgrades)
- kube-prometheus-stack:

  `helm template kube-prometheus-stack prometheus-community/kube-prometheus-stack --version 15.4.4 --include-crds --namespace monitoring --values ../../../base/kube-prometheus-stack/kube-prometheus-stack-values.yaml > ../../../base/kube-prometheus-stack/kube-prometheus-stack.yaml`

- loki-stack 
    
  `helm template loki-stack grafana/loki-stack  --set grafana.enabled=false,prometheus.enabled=false,prometheus.alertmanager.persistentVolume.enabled=false,prometheus.server.persistentVolume.enabled=false > ../../../base/loki-stack/loki-stack.yaml`

## Notes:
1. Monitoring has been configured and tested for K3D based off of this [K3S Monitoring](https://github.com/cablespaghetti/k3s-monitoring) guide.
2. Monitoring for [kube-controller-manager, kube-scheduler](https://github.com/cablespaghetti/k3s-monitoring/issues/2) and [etcd](https://github.com/cablespaghetti/k3s-monitoring/issues/4) have been disabled.

  
### TODO:
- ~~Setup Promtail.~~
- ~~Add Loki and Traefik dashboards to Grafana and provide documentation~~.
- Setup and use persistent storage.
- Create mainnet overlay.
- ~~Ingress/Endpoints for Grafana.~~
- Configure alerting.
- Setup Dandelion specific alerting rules.