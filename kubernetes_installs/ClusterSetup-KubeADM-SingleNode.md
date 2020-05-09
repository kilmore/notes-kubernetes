Assumes CentOS 7.x or REHL 7.x

https://docs.projectcalico.org/v3.2/getting-started/kubernetes/


## Step-1: Docker, Kubelet, kubectl and kubeadm on all nodes
### - Install Docker on all nodes
```
yum-config-manager --enable rhui-REGION-rhel-server-extras
yum install -y docker
systemctl enable docker && systemctl start docker
```
### - Install Kubeadm, kubelet, kubectl
- kubeadm: the command to bootstrap the cluster.
- kubelet: the component that runs on all of the machines in your cluster and does - things like starting pods and containers.
- kubectl: the command line util to talk to your cluster

Set the kubernets repository
```
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kube*
EOF

setenforce 0 #This temporarily disables SE-Linux

edit /etc/sysconfig/selinux
Change 
  SELINUX=enforcing to SELINUX=disabled

yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
systemctl enable kubelet && systemctl start kubelet
```

Route traffic correctly because iptables being bypassed. Set the net.bridge.bridge-nf-call-iptables is set to 1 in your sysctl config.
```
cat <<EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sysctl --system
```

## Step-2: Initialize the 
Setup the master
```
sudo kubeadm init --pod-network-cidr=10.244.0.0/16

kubeadm init --pod-network-cidr=10.244.0.0/16 --kubernetes-version=1.11.3

```

You should get a response like below:
```
To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of machines by running the following on each node
as root:

  kubeadm join 10.0.1.142:6443 --token *temporary_token* --discovery-token-ca-cert-hash *has of the ca cert*
```

## Step-3: Setup the network (in this case calico)
Setup a network

kubectl apply -f https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/rbac-kdd.yaml 

kubectl apply -f   https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/kubernetes-datastore/calico-networking/1.7/calico.yaml

kubectl get pods --all-namespaces


# Disable SE Linux
setenforce 0

and 

vi /etc/sysconfig/selinux 
Change SELINUX=enforcing to SELINUX=disabled

# Calico update
https://docs.projectcalico.org/v3.2/getting-started/kubernetes/



