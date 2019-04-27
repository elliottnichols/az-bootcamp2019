# Deploy Istio with Helm

## Download and Deploy Istio
```
$ cd 3-Istio
$ curl -L https://git.io/getLatestIstio | ISTIO_VERSION=1.1.4 sh -
$ kubectl apply -f istio-system.yaml

$ helm install istio-1.1.4/install/kubernetes/helm/istio --name istio --namespace istio-system \
    --values istio-1.1.4/install/kubernetes/helm/istio/values-istio-demo.yaml

# Verify Services look similiar
$ kubectl get svc -n istio-system
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                                                                                                                                      AGE
grafana                  ClusterIP      10.0.236.148   <none>          3000/TCP                                                                                                                                     12m
istio-citadel            ClusterIP      10.0.164.164   <none>          8060/TCP,15014/TCP                                                                                                                           12m
istio-egressgateway      ClusterIP      10.0.110.90    <none>          80/TCP,443/TCP,15443/TCP                                                                                                                     12m
istio-galley             ClusterIP      10.0.68.77     <none>          443/TCP,15014/TCP,9901/TCP                                                                                                                   12m
istio-ingressgateway     LoadBalancer   10.0.94.116    23.100.68.101   15020:31152/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:30097/TCP,15030:31793/TCP,15031:31143/TCP,15032:30901/TCP,15443:31129/TCP   12m
istio-pilot              ClusterIP      10.0.140.113   <none>          15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       12m
istio-policy             ClusterIP      10.0.78.180    <none>          9091/TCP,15004/TCP,15014/TCP                                                                                                                 12m
istio-sidecar-injector   ClusterIP      10.0.84.107    <none>          443/TCP                                                                                                                                      12m
istio-telemetry          ClusterIP      10.0.193.14    <none>          9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       12m
jaeger-agent             ClusterIP      None           <none>          5775/UDP,6831/UDP,6832/UDP                                                                                                                   12m
jaeger-collector         ClusterIP      10.0.107.53    <none>          14267/TCP,14268/TCP                                                                                                                          12m
jaeger-query             ClusterIP      10.0.119.178   <none>          16686/TCP                                                                                                                                    12m
kiali                    ClusterIP      10.0.19.159    <none>          20001/TCP                                                                                                                                    12m
prometheus               ClusterIP      10.0.79.125    <none>          9090/TCP                                                                                                                                     12m
tracing                  ClusterIP      10.0.41.109    <none>          80/TCP                                                                                                                                       12m
zipkin                   ClusterIP      10.0.98.120    <none>          9411/TCP

$ Verify Pods looks similiar.
$ kubectl get pods -n istio-system
NAME                                      READY   STATUS      RESTARTS   AGE
grafana-77b49c55db-mqxwg                  1/1     Running     0          14m
istio-citadel-7dc4f9c7b6-97qxd            1/1     Running     0          14m
istio-egressgateway-7554f75f6f-wmtvn      1/1     Running     0          14m
istio-galley-5d5d466cf5-phtvc             1/1     Running     0          14m
istio-ingressgateway-5f5dffc75d-mrw6q     1/1     Running     0          14m
istio-init-crd-10-prlk8                   0/1     Completed   0          91m
istio-init-crd-11-98dtv                   0/1     Completed   0          91m
istio-pilot-69f954bd48-958zr              2/2     Running     0          14m
istio-policy-7789cb8f5d-vbbbm             2/2     Running     4          14m
istio-sidecar-injector-7c8788b795-6zv4z   1/1     Running     0          14m
istio-telemetry-79b58dccd4-qlfhb          2/2     Running     3          14m
istio-tracing-595796cf54-pv4lw            1/1     Running     0          14m
kiali-5c584d45f6-l4m54                    1/1     Running     0          14m
prometheus-7f87866f5f-xr528               1/1     Running     0          14m
```

## Explore Kiali UI
```
$ kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```
Open UI in browser: http://localhost:20001/kiali/console  
Login with admin / admin

## Explore Istio Grafana Dashboards
Both Prometheus and Grafana in istio-system were deployed via Helm chart. Custom configuration would be required to configure Istio to use existing services.
```
$ kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```
Open UI in browser: http://localhost:3000

## Deploy Bookinfo App
Example book review app deployment for exploring and testing Iseio features.

![Bookinfo arch](https://istio.io/docs/examples/bookinfo/withistio.svg)


```
# Review bookinfo standard deployment configuration. Basic Services and Deployments for eachmicro-service.
$ cat istio-1.1.4/samples/bookinfo/platform/kube/bookinfo.yaml
# Run bookinfo.yaml to inject 

# Enable automatic sidecar injection namespace bookinfo 
$ kubectl label namespace bookinfo istio-injection=enabled
$ kubectl apply -f istio-1.1.4/samples/bookinfo/platform/kube/bookinfo.yaml -n bookinfo
$ kubectl get all -n bookinfo -w   # until objecs are availabe, ready, or running 

# Curl ratings service
$ kubectl exec -it $(kubectl get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}') -c ratings -- curl productpage:9080/productpage | grep -o "<title>.*</title>"

# Setup Gateway
$ kubectl apply -f istio-1.1.4/samples/bookinfo/networking/bookinfo-gateway.yaml -n bookinfo
$ export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
$ export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')
$ export SECURE_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="https")].port}')
$ export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT

## Bug: https://github.com/Azure/AKS/issues/643
# Can't configure AKS use use Standard LB
# Associate LB Backend Pool to Virtual Machine Scale Set
```