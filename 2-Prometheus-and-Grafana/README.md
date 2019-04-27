# Prometheus
By installing kube-prometheus you'll get 1 Prometheus server  and a set of service monitor resources that allow you to monitor THE cluster itself. Installing kube-prometheus also gives you a Grafana instance that is connected to the prometheus server and has a set of dashboards pre-configured.

```bash
$ cd ../2-Prometheus-and-Grafana
$ kubectl create namespace monitoring

# Add Prometheus operator and server components
$ helm install coreos/prometheus-operator --name prometheus-operator --namespace monitoring
$ helm install coreos/kube-prometheus --name kube-prometheus --namespace monitoring


# Confirm Prometheus Operator
$ kubectl get prometheus --all-namespaces -l release=kube-prometheus
NAMESPACE    NAME              AGE
monitoring   kube-prometheus   14s

# Confirm Service Monitor Resources
$ kubectl get servicemonitor --all-namespaces -l release=kube-prometheus
NAMESPACE    NAME                                               AGE
monitoring   kube-prometheus                                    43s
monitoring   kube-prometheus-alertmanager                       43s
monitoring   kube-prometheus-exporter-kube-controller-manager   43s
monitoring   kube-prometheus-exporter-kube-dns                  43s
monitoring   kube-prometheus-exporter-kube-etcd                 43s
monitoring   kube-prometheus-exporter-kube-scheduler            43s
monitoring   kube-prometheus-exporter-kube-state                43s
monitoring   kube-prometheus-exporter-kubelets                  43s
monitoring   kube-prometheus-exporter-kubernetes                43s
monitoring   kube-prometheus-exporter-node                      43s
monitoring   kube-prometheus-grafana                            43s

```

## Browse and explore Prometheus
```
# Setup port-forward to prometheus port
$ kubectl --namespace monitoring port-forward $(kubectl get pod --namespace monitoring -l prometheus=kube-prometheus -l app=prometheus -o template --template "{{(index .items 0).metadata.name}}") 9090:9090
```
Browse to http://localhost:9090


## Browse and Explore Grafana

- Configure port-forward to Grafana UI
```
# Setup port-forward to Grafana port
$ kubectl --namespace monitoring port-forward $(kubectl get pod --namespace monitoring -l app=kube-prometheus-grafana -o template --template "{{(index .items 0).metadata.name}}") 3000:3000
```

 - Browse to http://localhost:3000
 - Select Arrow Icon in lower left to Sign In: `admin/admin`
 - Explore Dashboards, Configuration, UI, etc.

