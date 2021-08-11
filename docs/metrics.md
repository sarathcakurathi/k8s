## K8S Metrics

K8S metrics come from 
1. Metrics server - collects resource usage stats from kubelet on each node and provide aggregated metrics through metrics api. Metrics Server stores only near-real-time metrics in memory, so it is primarily valuable for spot checks of CPU or memory usage, or for periodic querying by a full-featured monitoring service that retains data over longer timespans. Google Kubernetes Engines clusters include a metrics server deployment by default. 

2. kube-state-metrics - a service that makes cluster state information easily consumable. listens to control plane api server for data on the overall status of kubernetes objects, as well as the resource limits and allocations for those objects. It then generate metrics from that data that are available through the metrics api.

### Key performance metrics to monitor

Run below command to see the metrics-server pods.

```
kubectl get pods --all-namespaces | grep metrics-server
```

### To deploy metrics server

```
git clone https://github.com/kubernetes-sigs/metrics-server.git
cd metrics-server
kubectl apply -f deploy/kubernetes/
```

### Kubectl get to query metrics api
Metrics API returns a JSON object

```
kubectl get --raw /apis/metrics.k8s.io/v1beta1/nodes/<NODE_NAME> | jq
kubectl get --raw /apis/metrics.k8s.io/v1beta1/namespaces/<NAMESPACE>/pods/<POD_NAME> | jq
```

### To query CPU and memory usage of a Kubernetes node
```
kubectl get --raw /apis/metrics.k8s.io/v1beta1/nodes/<NODE_NAME> | jq

{
  "kind": "NodeMetrics",
  "apiVersion": "metrics.k8s.io/v1beta1",
  "metadata": {
    "name": "<>",
    "selfLink": "/apis/metrics.k8s.io/v1beta1/nodes/<NODE_NAME>",
    "creationTimestamp": "2019-12-10T18:34:01Z"
  },
  "timestamp": "2019-12-10T18:33:41Z",
  "window": "30s",
  "usage": {
    "cpu": "62789706n",
    "memory": "641Mi"
  }
}
```

### Metrics snapshot

```
kubectl top node

kubectl top pod --namespace myns

# Resource breakdown for a given pod
kubectl top pod <pod_name> --namespace kube-system --containers
```