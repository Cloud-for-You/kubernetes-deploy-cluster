## How create __k8s__ multi-cluster in development environment.

__Cluster name__
- k8s-c1
- k8s-c2

### Network configuration
All cluster is shared network from IP pool 100.64.0.0 – 100.127.255.255

Every cluster has uniq IP pool from shared network
- PodCIDR: /25
- ServiceCIDR: /25


### Compute Infrastructure
For virtualization using MacMini M1 with 8Core and 16GB RAM

1. Deploy VM in multipass for every cluster
   - OS Ubuntu latest (1 master and 1 worker)

### Configuration k8s clusters
- Overlay network build in Cilium (helm values for deployment cilium is [here](networking/cilium))
