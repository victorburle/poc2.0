# poc2.0
Create Namespaces
```
kubectl create namespace app
```

```
kubectl create namespace monitoring
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
