# ETCD

A distributed reliavel ke-value store that is Simple, Secure and Fast

Stores information on:
* Nodes
* PODs
* Configs
* Secrets
* Accounts
* Roles
* Bindings
* Other.....

Every change made to the cluster is stored in ETC. Only once the change is persisted to ETCd is the change considered to be "complete"

Kubernetes stores its information in the "registry" root key path
-- /registry/apiregistration.k8s.io/apiservices/...
-- you can see the whole list by running `etcdctl get / --prefix -keys-only`

listens on 2379

mkdir etcd
curl https://github.com/etcd-io/etcd/releases/download/v3.4.7/etcd-v3.4.7-linux-amd64.tar.gz -o etcd/etcd-v3.4.7-linux-amd64.tar.gz

