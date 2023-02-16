## Prometheus Operator 快速部署


### 1.Prometheus Operator
```
    Prometheus Operator 目前功能最全面的开源监控方案。 能够监控Node Port ，并支持集群的各种管理组件，如 API Server 、Scheduler、Controller Manager等。
    Prometheus Operator 的目标是尽可能简化在 Kubernetes 中部署和维护 Prometheus 的工作。
```
### 2.部署Prometheus Operator
```
    git clone  https://github.com/prometheus-operator/kube-prometheus.git
    kubectl create -f  kube-prometheus/manifests/setup             #创建命名空间和CRD
    kubectl create -f  kube-prometheus/manifests/                  #创建资源
```
### 3.端口转发(由于平台在集群外部运行，没做服务暴露可以转发提供测试)
```
    kubectl port-forward svc/prometheus-k8s  --address  0.0.0.0  9090:9090   -n monitoring
```
