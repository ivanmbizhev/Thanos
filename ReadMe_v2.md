### Treat with great care! This whole configuration was made with the idea of using ArgoCD if using pipelines managing the whole architecture will be harder and prone to issues and human error.

# 🚀 Observability & GitOps Stack with Thanos, Kube-Prometheus-Stack, Loki, Tempo & ArgoCD

This repository sets up a comprehensive observability and GitOps-driven deployment stack for Kubernetes environments using the following components:

- **[Thanos](https://thanos.io/)** — Highly available Prometheus metrics with long-term storage
- **[Kube-Prometheus-Stack](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack)** — Kubernetes monitoring via Prometheus, Alertmanager, Grafana
- **[Loki](https://grafana.com/oss/loki/)** — Scalable, multi-tenant log aggregation system
- **[Tempo](https://grafana.com/oss/tempo/)** — Distributed tracing backend
- **[ArgoCD](https://argo-cd.readthedocs.io/)** — GitOps continuous delivery for Kubernetes


---

## 🔧 Stack Components

### 📈 Kube-Prometheus-Stack
- **Prometheus**: Scrapes metrics from Kubernetes components, applications, and exporters.
- **Alertmanager**: Handles alerts sent by Prometheus.
- **Grafana**: Visualizes metrics, logs, and traces.
- **Node Exporter/Kube-State-Metrics**: Provides node and K8s object-level metrics.

### 🧠 Thanos
- Adds **HA** and **long-term storage** to Prometheus.
- Components used:
  - **Thanos Sidecar** (deployed alongside Prometheus)
  - **Thanos Store**, **Compactor**, **Querier**, and **Bucket** components (typically in object storage like S3, GCS)

### 📜 Loki
- Lightweight, Prometheus-inspired log aggregation system.
- Pushes logs using **Promtail** or **FluentBit**.
- Easily queryable via Grafana using LogQL.

### 🌌 Tempo
- Distributed tracing backend compatible with OpenTelemetry, Jaeger, and Zipkin.
- Correlates logs and traces in Grafana (via TraceQL or using traceID lookups from logs).

### 🚀 ArgoCD
- Manages deployment and lifecycle of Kubernetes manifests.
- Enables GitOps workflows: you push to Git, ArgoCD applies the change.
- Can manage all of the above as Helm charts or Kustomize apps.

---

## 🧩 How Components Work Together

```plaintext
               +----------------------+
               |      ArgoCD         |
               | (GitOps Deployments)|
               +----------+----------+
                          |
                          v
+--------------------- Kubernetes Cluster ----------------------+
|                                                               |
|  +-------------------+   +-------------------+               |
|  |  Prometheus (HA)  |<--| Kube-State-Metrics|               |
|  | + Thanos Sidecar  |   +-------------------+               |
|  +--------+----------+               ^                       |
|           |                          |                       |
|           v                          |                       |
|    +------+--------+     +--------------------+              |
|    | Thanos Store  |<--->|  Node Exporter     |              |
|    +---------------+     +--------------------+              |
|           ^                                                |
|           |                                                |
|    +------+--------+                                       |
|    | Thanos Compactor |                                    |
|    +------------------+                                    |
|           ^                                                |
|           |                                                |
|    +------+--------+     +--------------------+            |
|    |  Object Store |<--->| Thanos Bucket      |            |
|    +---------------+     +--------------------+            |
|                                                               |
|    +-------------------+  +------------------+               |
|    | Loki + Fluent-bit   |  |  Tempo Collector |             |
|    +-------------------+  +------------------+               |
|              |                    |                          |
|              v                    v                          |
|        +-----------+       +------------+                   |
|        |   Grafana  |<----->|  Data Sources |                |
|        +-----------+       +------------+                   |
|               ^                                           |
|               |                                           |
|       Dashboards, Logs, Traces, Alerts                    |
+-----------------------------------------------------------+
