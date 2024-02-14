### Install
```
helm install cilium cilium/cilium --version 1.15.0 \
  --namespace kube-system
```

### Enable Ingress
```
helm upgrade cilium cilium/cilium --version 1.15.0 \
  --namespace kube-system \
  --reuse-values \
  --set ingressController.enabled=true \
  --set ingressController.loadbalancerMode=shared \
  --set ingressController.service.type=NodePort \
  --set ingressController.service.insecureNodePort=30080 \
  --set ingressController.service.secureNodePort=30443
kubectl -n kube-system rollout restart deployment/cilium-operator
kubectl -n kube-system rollout restart ds/cilium
```

### Enable Service Mesh
***CLUSTER_NAME*** and ***CLUSTER_ID*** is unique in every cluster
##### For each cluster
```
helm upgrade cilium cilium/cilium --version 1.15.0 \
  --namespace kube-system \
  --reuse-values \
  --set cluster.name${CLUSTER_NAME} \
  --set cluster.id=${CLUSTER_ID}
kubectl -n kube-system rollout restart deployment/cilium-operator
kubectl -n kube-system rollout restart ds/cilium
```

### Shared Certificate Authority
##### Get certificate from first cluster
```
kubectl get secret -n kube-system cilium-ca -o jsonpath='{.data.ca\.crt}' | base64 -d > ca.crt
kubectl get secret -n kube-system cilium-ca -o jsonpath='{.data.ca\.key}' | base64 -d > ca.key
```
##### Apply to second cluster
```
kubectl delete secret -n kube-system cilium-ca
kubectl create secret generic cilium-ca -n kube-system --from-file=ca.crt=networking/cilium/ca.crt --from-file=ca.key=networking/cilium/ca.key
```

### Enable service
In each clusters
```
cilium clustermesh enable --service-type=LoadBalancer
```
