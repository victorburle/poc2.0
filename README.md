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
Install Jaeger
```
kubectl -n istio-system apply -f https://raw.githubusercontent.com/istio/istio/release-1.22/samples/addons/jaeger.yaml
```
Install RabbitMQ
```
helm install rabbitmq -n app bitnami/rabbitmq --values helm/rabbitmq/values.yaml
```

Install Redis
```
helm install redis -n app bitnami/redis --values helm/redis/values.yaml
```

Install Kiali
```
helm repo add kiali https://kiali.org/helm-charts
```
Update Repo

```
helm repo update
```
Install Kiali

```
helm install --set cr.create=true --set cr.namespace=istio-system --set cr.spec.auth.strategy="anonymous" --namespace kiali-operator --create-namespace kiali-operator kiali/kiali-operator
```
Expose Kiali
```
kubectl -n istio-system  port-forward service/kiali 20001:20001
```
Install Kube-Prometheus-Stack

Add repo
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```
Install
```
helm install -n monitoring kube-prometheus-stack -n monitoring prometheus-community/kube-prometheus-stack --values helm/kube-prometheus-stack/values.yaml
```
Upgrade
```
helm upgrade -n monitoring kube-prometheus-stack -n monitoring prometheus-community/kube-prometheus-stack --values helm/kube-prometheus-stack/values.yaml
```

Expose Prometheus

```
kubectl port-forward service/kube-prometheus-stack-prometheus 8090:9090
```

Install Loki
```
helm install loki -n monitoring grafana/loki-stack --set grafana.enabled=false
```

Install Lab
```
kubectl apply -f lab/
```

Login RabbitMQ
```
user: user
password: kvs rabbitmq rabbitmq-password
```

Create Exchanges for the queues and bind them
```
checkout_ex
order_ex
payment_ex
```

Inject Istio Sidecar

```
kubectl label namespace app istio-injection=enabled --overwrite
```

Restart the pods to apply sidecar
```
kubectl rollout restart deployment catalog checkout order orderpayment payment product

kubectl rollout restart statefulset.apps/rabbitmq    

kubectl rollout restart statefulset.apps/redis-master statefulset.apps/redis-replicas
```

Expose Grafana
```
kubectl port-forward -n monitoring  service/kube-prometheus-stack-grafana 8080:80 
```

Install X509 Cert exporter
```
helm install -n app x509-certificate-exporter-app enix/x509-certificate-exporter
kubectl create namespace certs
helm install -n certs x509-certificate-exporter-certs enix/x509-certificate-exporter
helm install -n monitoring x509-certificate-exporter-monitoring enix/x509-certificate-exporter
```

