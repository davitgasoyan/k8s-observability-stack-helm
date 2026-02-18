# Kubernetes Observability Stack Helm Chart

This Helm chart deploys a complete Kubernetes observability stack with **Grafana**, **Loki**, **Prometheus**, and **cAdvisor**. It's designed for on-prem or lab environments and can be customized via `values.yaml`.

---

## Features

- **Grafana**: Dashboarding for metrics and logs

- **Loki**: Centralized log aggregation

- **Prometheus**: Metrics collection and alerting

- **cAdvisor**: Node-level container metrics

- Supports NFS-backed Persistent Volumes

- Fully configurable via `values.yaml`

---

## Prerequisites

- Kubernetes cluster (v1.26+ recommended)

- Helm v3+

- NFS server

- `kubectl` configured

## Installation

### 1. Clone this repository

```bash
git clone https://github.com/davitgasoyan/k8s-observability-stack-helm
cd k8s-observability-stack-helm
```

### 2. Customize values.yaml

Edit the values.yaml file to specify:

* namespace: Kubernetes namespace for all resources

* Grafana credentials and TLS

* NFS server and paths for persistent storage

* Loki and Prometheus storage

* Scrape targets for Prometheus

Example snippet:

```yaml
namespace: logging

grafana:
    adminUser: admin
    adminPassword: SuperSecret123
    nodePort: 32000
    tls:
        crt: "<base64 cert>"
        key: "<base64 key>"
    storage:
        nfsServer: "10.0.0.10"
        nfsPath: "/export/grafana"
        size: 5Gi

prometheus:
    storage:
        nfsServer: "10.0.0.10"
        nfsPath: "/export/prometheus"
        size: 10Gi

scrapeTargets:
    kubelet:
    - "10.0.0.1:10255"
    kubeStateMetrics:
    - "10.0.0.1:32119"

loki:
    storage:
        nfsServer: "10.0.0.10"
        nfsPath: "/export/loki"
        size: 5Gi
```

### 3. Install the chart

```bash
helm install obs-stack ./ -f values.yaml
```

### 4. Verify installation

```bash
kubectl get pods --namespace logging
kubectl get svc --namespace logging
```

### 5. Access Grafana

**Grafana** is exposed via `NodePort` service on the port specified in `values.yaml` (default 32000). **Loki** and **Prometheus** are accessible via cluster services.

## Usage Notes

- Grafana credentials come from the values.yaml secret (base64 encoded)

- TLS is optional; leave empty if not using HTTPS

- NFS paths must exist and be accessible by your cluster nodes

- Adjust Prometheus scrape targets according to your cluster IPs and services

## Uninstallation

```bash
helm uninstall k8s-observability-stack -n logging
kubectl delete ns logging
```
