# poc2.0
Create Namespaces
```
kubectl create namespace app
kubectl create namespace monitoring

```

Install RabbitMQ
```
helm install rabbitmq -n app oci://registry-1.docker.io/bitnamicharts/rabbitmq --values /poc2.0/helm/rabbitmq/values.yaml
```

Install Redis
```
helm install redis -n app oci://registry-1.docker.io/bitnamicharts/redis --values /poc2.0/helm/redis/values.yaml
```

Install Kube-Prometheus-Stack
```
helm upgrade --install -n monitoring kube-prometheus-stack -n monitoring prometheus-community/kube-prometheus-stack --values /poc2.0/helm/kube-prometheus-stack/values.yaml
```

Install Loki
```
helm install loki -n monitoring grafana/loki-stack --set grafana.enabled=false
```
