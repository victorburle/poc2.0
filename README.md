# poc2.0
Create HA Kind Cluster with provided config

```
kind create cluster --config kind/config.yaml
```
Create App Namespaces
```
kubectl create namespace app
```

```
kubectl create namespace monitoring
```

Add the repo and update it
```
helm repo add istio https://istio-release.storage.googleapis.com/charts
helm repo update
```

Create Istio System  Namespaces

```
kubectl create namespace istio-system
```
Install Istio 
```
helm install istio-base istio/base -n istio-system --set defaultRevision=default
helm install istiod istio/istiod -n istio-system --wait
```
Install Custom Prometheus for Istio
```
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.22/samples/addons/prometheus.yaml
```
Create Istio Ingress  Namespaces
```
kubectl create namespace istio-ingress
```
Install Istio Ingress
```
helm install istio-ingress istio/gateway -n istio-ingress 
```

Install RabbitMQ
```
helm install rabbitmq -n app bitnami/rabbitmq --values helm/rabbitmq/values.yaml -n app
```

Install Redis
```
helm install redis -n app bitnami/redis --values helm/redis/values.yaml
```

Kube-Prometheus-Stack

Add repo
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```
Install
```
helm install -n monitoring kube-prometheus-stack -n monitoring prometheus-community/kube-prometheus-stack --values helm/kube-prometheus-stack/values.yaml
```

Install Loki
```
helm install loki -n monitoring grafana/loki-stack --set grafana.enabled=false
```
