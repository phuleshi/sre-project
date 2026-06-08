# Prometheus – ArgoCD Installation

Cài **kube-prometheus-stack** (Prometheus + Alertmanager + Grafana + Node Exporter + kube-state-metrics) lên Kubernetes thông qua ArgoCD.

---

## Cấu trúc thư mục

```
prometheus/
├── application.yaml   # ArgoCD Application manifest
├── values.yaml        # Helm values tùy chỉnh
└── README.md
```

---

## Cách cài đặt

### 1. Thêm Helm repo (tuỳ chọn – để test local)

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

### 2. Apply ArgoCD Application

```bash
kubectl apply -f prometheus/application.yaml
```

ArgoCD sẽ tự tạo namespace `monitoring` và cài chart vào đó.

### 3. Kiểm tra trạng thái

```bash
# Xem App trong ArgoCD
argocd app get prometheus

# Xem pods
kubectl get pods -n monitoring

# Xem services
kubectl get svc -n monitoring
```

---

## Truy cập Grafana

```bash
# Port-forward tạm thời
kubectl port-forward svc/prometheus-grafana 3000:80 -n monitoring
```

Mở trình duyệt: `http://localhost:3000`  
Tài khoản mặc định: `admin / admin` (đổi trong `values.yaml` → `grafana.adminPassword`)

---

## Truy cập Prometheus UI

```bash
kubectl port-forward svc/prometheus-kube-prometheus-prometheus 9090:9090 -n monitoring
```

Mở trình duyệt: `http://localhost:9090`

---

## Tuỳ chỉnh thường gặp

| Mục | File | Key |
|-----|------|-----|
| Đổi mật khẩu Grafana | `values.yaml` | `grafana.adminPassword` |
| Bật Ingress Grafana | `values.yaml` | `grafana.ingress` |
| Tăng retention | `values.yaml` | `prometheus.prometheusSpec.retention` |
| StorageClass | `values.yaml` | tìm `storageClassName` |
| Thêm Alertmanager receiver | `values.yaml` | `alertmanager.config.receivers` |

---

## Nâng cấp version

Sửa `targetRevision` trong `application.yaml`, sau đó:

```bash
kubectl apply -f prometheus/application.yaml
argocd app sync prometheus
```
