# Data Engineering

## Pre-requisites

### Kubernetes

Follow the steps here to [install](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-using-native-package-management) `kubectl`.

### k3d

```bash
curl -s https://raw.githubusercontent.com/k3d-io/k3d/main/install.sh | bash
```

#### Cluster setup

```bash
k3d cluster create --config cluster.yaml
k3d cluster start data-engineering
```

#### Using a dedicated namespace

We'll create and use separate namespaces for individual components of `data-engineering` cluster.

```bash
kubectl create namespace analytics
kubectl create namespace monitoring
kubectl create namespace visualizer
```

### Helm 3 (Ubuntu/Debian)

```bash
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```

#### Charts

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

## Installation

**Note:** Make sure that `data-engineering` cluster is up and running.

### Spark

```bash
helm install spark bitnami/spark -f helm/spark.yaml
```

### Prometheus

```bash
kubectl apply -f k8s/prometheus.yaml
helm install --namespace monitoring prometheus prometheus-community/prometheus -f helm/prometheus.yaml
```

### Grafana

```bash
kubectl apply -f k8s/grafana.yaml
helm install grafana grafana/grafana -f helm/grafana.yaml
```
