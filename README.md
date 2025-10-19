# Kubeflow Pipelines Setup Guide

This guide shows how to set up Kubeflow Pipelines on a local Kind cluster for ML pipeline development and demos.

## Prerequisites

- Docker Desktop
- macOS/Linux terminal
- 8GB+ RAM recommended

## 1. Install Kind (Kubernetes in Docker)

```bash
# Install Kind
brew install kind

# Verify installation
kind version
```

## 2. Create Kind Cluster

```bash
# Create single-node cluster
kind create cluster --name kind-cluster

# Verify cluster is running
kubectl cluster-info --context kind-kind-cluster
```

## 3. Install Helm

```bash
# Install Helm
brew install helm

# Verify installation
helm version
```

## 4. Install Kubeflow Pipelines

```bash
# Add GetInData Helm repository
helm repo add getindata https://getindata.github.io/helm-charts/
helm repo update

# Install Kubeflow Pipelines
helm install my-kubeflow-pipelines getindata/kubeflow-pipelines \
  --version 1.6.2 \
  --set platform.managedStorage.enabled=false \
  --set platform.cloud=gcp \
  --set platform.gcp.proxyEnabled=false \
  --namespace kubeflow-pipelines \
  --create-namespace

# Wait for pods to be ready
kubectl get pods -n kubeflow-pipelines
```

## 5. Access Kubeflow UI

```bash
# Port forward to access UI
kubectl port-forward -n kubeflow-pipelines svc/ml-pipeline-ui 8080:80

# Open browser: http://localhost:8080
```

## 6. Setup Python Environment

```bash
# Create project directory
mkdir kfp
cd kfp

# Create virtual environment
python3 -m venv .kfp
source .kfp/bin/activate

# Install KFP SDK
pip install kfp==2.14.6

# Verify installation
python3 -c "import kfp; print(f'KFP version: {kfp.__version__}')"
```

## 7. Create ML Pipeline

Create `simple_demo.yaml` with the ML pipeline (already provided in this repo).

## 8. Upload Pipeline to Kubeflow

1. Open http://localhost:8080
2. Click "Upload Pipeline"
3. Select `simple_demo.yaml`
4. Click "Upload"
5. Run the pipeline

## Pipeline Overview

The `simple_demo.yaml` contains a complete ML pipeline with:

- **Data Creation**: Generate 1000 samples with 3 features
- **Data Splitting**: 80/20 train/test split
- **Model Training**: Rule-based classifier (sum > 15)
- **Model Testing**: Calculate accuracy, precision, recall, F1
- **Report Generation**: Comprehensive ML report

## Troubleshooting

### Pods not starting
```bash
# Check pod status
kubectl get pods -n kubeflow-pipelines

# Check pod logs
kubectl logs <pod-name> -n kubeflow-pipelines
```

### UI not accessible
```bash
# Restart port forwarding
kubectl port-forward -n kubeflow-pipelines svc/ml-pipeline-ui 8080:80
```

### Clean restart
```bash
# Delete cluster and recreate
kind delete cluster --name kind-cluster
kind create cluster --name kind-cluster
```

## Files

- `simple_demo.yaml` - Complete ML pipeline for Kubeflow
- `.kfp/` - Python virtual environment
- `README.md` - This guide

## Next Steps

- Modify the pipeline for your specific ML use case
- Add more complex models (scikit-learn, TensorFlow, etc.)
- Integrate with data sources and model registries
- Deploy to production Kubernetes clusters

---

**Perfect for Medium articles and ML demos!** 🚀
