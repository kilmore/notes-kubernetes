# Kubernetes Offline Install (aka Air Gap)
# __WARNING__ - This is a first draft. There WILL be errors. 

## __Install Notes__ ----------------------------------------
* Operating System and Environment

     * This install is done  a RHEL 7.5 on AWS. The approach "should" work for any verison of linux, swap in your own commands.
     * The internet connected instance is being created from the same image the air gapped on is. This is VERY important.This means they have all the same dependancies already installed. If you can't do this then you'll need to swap out the yum install --downloadonly command for yumdownloader --installroot... (there is much more you need to google on that command)
     * The difference is that the yum install --downloadonly will download only the dependances not installed. the yumdownloader --installroot will download EVERYTHING YOU NEED, plus somethings you don't (i686 rpm's in addition to the i386, for example).

* Local RPM Repositories:

     In this guide we create a local RPM repository on each server. We do this so we can just "dump" all rpm's we need into a single directory and use the rpm repo to deal with the rpm dependecys. 

* Kubernetes Network

     In this guide I have chosen to use Calico as the CNI. That is purely because I prefer Calico. If you prefere a different CNI (e.g. Flannel, Weave, etc.) then you will swap in the appropriate configuration files and docker images. 

* Kubernetes Version

     This guide installes kuberenetes v1.11.3 and the most current version of etcd, busy box, etc. By the time you read this, there will newer versions of each. Simple swap in the version you are using in the appropriate places. 

* Docker Images
     There really is no magic to figuring out what docker images are needed. The simplest way is to do a Kubernetes install via KubeADM. You can then use `docker images ls` to identify the container images you need. In this guide we use `kubeadm config images pull` to download the base images and then reference an existing stack for the "other ones".  

* Running the Commands as root 

     By default expect to run all commands below as root, unless the instructions explicitly call out running as mortal user. 


Steps:
1.  Gather all the install "stuff"
2. Configure and install on master
3. Configure and install on worker
4. Have a tasty beverage and snack (orange soda and gummy frogs are the order of the day).

## __Gather The install packages__ ----------------------------------------
You will need to create an instance that connects to the interwebs (aka internet). On this system we will download the RPMs needed for install, docker images and network configuration files. 

Set the RHEL repository. This only needs to be done on the server connected to the interwebs
```
 yum-config-manager --enable rhui-REGION-rhel-server-extras
```

Create a kubernetes repo, this is used to download the needed kubernetes RPMs
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

setenforce 0

```

Create a place to store the various bits we will need
```
# Make a root folder. 
mkdir k8offline && cd k8offline
mkdir rpms 
mkdir containers
mkdir cni
cd ..
``` 

Download the RPMS
```
# Base and Docker RPMs
yum install -y --downloadonly --downloaddir=k8offline/rpms/ wget zip unzip createrepo yum-utils device-mapper-persistent-data docker

# Kubernetes RPMs
yum install -y --disableexcludes=kubernetes --downloadonly --downloaddir=k8offline/rpms/ kubelet kubeadm kubectl
```

We need to install the RPM's on the internet connected system. At a minimum we need docker (to download the container images)
```
mkdir /lib/LocalRepo/MyRPMs
cp k8offline/rpms/*.rpm /lib/LocalRepo/MyRPMs

# install the create repo package (and its two dependancies first)
yum install -y /lib/LocalRepo/MyRPMs/deltarpm-3.6-3.el7.x86_64.rpm
yum install -y /lib/LocalRepo/MyRPMs/python-deltarpm-3.6-3.el7.x86_64.rpm
yum install -y /lib/LocalRepo/MyRPMs/createrepo-0.9.9-28.el7.noarch.rpm

# Now configure the new local repository the place where the repo's will live
# This is the directory we created earlier and downloaded the RPMs to
createrepo /lib/LocalRepos/MyRPMs

# Create the repository config file
cat <<EOF > /etc/yum.repos.d/LocalInstall.repo
[MyRPMs]
name=LocalRepo
baseurl=file:///lib/LocalRepos/MyRPMs
enabled=1
gpgcheck=0
EOF


# Install the kit and kaboodle
sudo yum install --disablerepo=* --enablerepo=MyRPMs /lib/LocalRepos/MyRPMs/*.rpm  -y
systemctl enable docker && systemctl start docker
```

Download the docker images
```
# Here we use kubeadm to download the docker images we need for v1.11.3
kubeadm config images pull --kubernetes-version=v1.11.3

# adn now pull the other supporting containers
docker image pull quay.io/calico/node:v3.1.3
docker image pull quay.io/calico/cni:v3.1.3
docker image pull k8s.gcr.io/coredns:1.1.3
docker image pull k8s.gcr.io/etcd-amd64:3.2.18
docker image pull k8s.gcr.io/pause:3.1
docker pull registry
docker pull busybox

# Save the container images 
docker image save k8s.gcr.io/kube-proxy-amd64:v1.11.3 > containers/kube-proxy-amd64_v1.11.3.tar
docker image save k8s.gcr.io/kube-scheduler-amd64:v1.11.3 > containers/kube-scheduler-amd64_v1.11.3.tar
docker image save k8s.gcr.io/kube-apiserver-amd64:v1.11.3 > containers/kube-apiserver-amd64_v1.11.3.tar
docker image save k8s.gcr.io/kube-controller-manager-amd64:v1.11.3 > containers/kube-controller-manager-amd64_v1.11.3.tar
docker image save quay.io/calico/node:v3.1.3 > containers/calico_node_v3.1.3.tar
docker image save quay.io/calico/cni:v3.1.3 > containers/calico_cni_v3.1.3.tar
docker image save k8s.gcr.io/coredns:1.1.3 > containers/coredns_1.1.3.tar
docker image save k8s.gcr.io/etcd-amd64:3.2.18 > containers/etc_amd64_3.2.18.tar
docker image save k8s.gcr.io/pause:3.1 > containers/paus_3.2.tar
docker image save registry > containers/registry.tar
docker image save busybox > containers/busybox.tar
```

Download the Calico configuration files
```
wget -O cni/rbac-kdd.yaml https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/rbac-kdd.yaml
wget -O calico.yaml https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/kubernetes-datastore/calico-networking/1.7/calico.yaml

```

Now tar it all up. 
tar -czvf k8offline.tar.gz k8offline/

## __Install on Master and Workers__ ---------------------------------------
You need to do the following on both the Master and the Worker.

1. Copy the tarball we created on the interweb connected system to the air gapped system and unpack it. This guide assumes you are in the K8Offline directory (its the root directory we created above).

2. Set network settings for Kubernetes. 
```
cat <<EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sysctl --system
```

3. Disable SELinux 
     Edit the /etc/sysconfig/selinux, this will take effect on next reboot 
    `Change SELINUX=enforcing to SELINUX=disabled`
    
     Disable SELinux in active sessions `setenforce 0` 


4. Create the local repository 

Configuration file. We will be setting up the repository shortly
```
cat <<EOF > /etc/yum.repos.d/LocalInstall.repo
[MyRPMs]
name=LocalRepo
baseurl=file:///lib/LocalRepos/MyRPMs
enabled=1
gpgcheck=0
EOF
```

Copy in the RPMs
```
mkdir -p /lib/LocalRepos/MyRPMs
cp rpms/*.rpms /lib/LocalRepos/MyRPMs
```

Install the CreateRepo Tool
```
yum install -y /lib/LocalRepos/MyRPMs/deltarpm-3.6-3.el7.x86_64.rpm
yum install -y /lib/LocalRepos/MyRPMs/python-deltarpm-3.6-3.el7.x86_64.rpm
yum install -y /lib/LocalRepos/MyRPMs/createrepo-0.9.9-28.el7.noarch.rpm
```

Create the repo
```
createrepo /lib/LocalRepos/MyRPMs
```

5.  Install the rpms  
We need to install the docker and kubernetes RPMs
```
sudo yum install --disablerepo=* --enablerepo=MyRPMs /lib/LocalRepos/MyRPMs/*.rpm  -y
```

6. Register and star Docker and Kubelet 
``` 
systemctl enable docker && systemctl start docker
systemctl enable kubelet && systemctl start kubelet
```

7. Import the Docker Images 
   docker load < busybox.tar
   docker load < calico_cni.tar
   docker load < calico_node.tar
   docker load < controller-manager.tar
   docker load < coredns.tar
   docker load < etc.tar
   docker load < kube-proxy_v1.11.3.tar
   docker load < kube-proxy_v1.11.3.tar pause.tar
   docker load < pause.tar
   docker load < registry_latest.tar
   docker load < scheduler.tar

Verify the images are loaded `docker images ls`

Now we are ready to do the installes. Take a breath, have a beer, ... 

## __Setup Master__ ---------------------------------------
Run the kubeadm init. Here we identify the specific version we are installing. The needed container images "should" be on the system. 
```
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --kubernetes-version=v1.11.3
```

The output of that command "should" provide an example on setting up a mere mortal user with kubectl access as well as the sample join command.

switch out of root to a mortal users (e.g. Zero_Cool.) Run the kube config setup. Once done as that user run the  following commands.

```
kubectl apply -f cni/rbac-kdd.yaml
kubectl apply -f cni/calico.yaml
```

To verify that all is well and right:
```
kubectl get pods
```

Output should look like
``` 
NAMESPACE     NAME                                                 READY     STATUS    RESTARTS   AGE
kube-system   calico-node-g6fqt                                    2/2       Running   0          8m
kube-system   coredns-78fcdf6894-5zttp                             1/1       Running   0          43m
kube-system   coredns-78fcdf6894-xqt29                             1/1       Running   0          43m
kube-system   etcd-ip-10-0-3-215.ec2.internal                      1/1       Running   0          42m
kube-system   kube-apiserver-ip-10-0-3-215.ec2.internal            1/1       Running   0          42m
kube-system   kube-controller-manager-ip-10-0-3-215.ec2.internal   1/1       Running   0          42m
kube-system   kube-proxy-6nc9l                                     1/1       Running   0          43m
kube-system   kube-scheduler-ip-10-0-3-215.ec2.internal            1/1       Running   0          42m
```

## __Setup Worker__ ---------------------------------------

Run the join command. Should look similar to:
```
kubeadm join 10.0.3.215:6443 --token hhxvqv.1r4jdge1c0urb12b --discovery-token-ca-cert-hash sha256:e2f6559527fd30c093e272070d4615098a7101add38cb4f5e07f6492a2cd78cc
```

Verify that the node joined correctly:
```
kubectl get nodes
```

Output should look like:
```
NAME                         STATUS    ROLES     AGE       VERSION
ip-10-0-3-193.ec2.internal   Ready     <none>    1m        v1.11.3
ip-10-0-3-215.ec2.internal   Ready     master    1h        v1.11.3
```
## Trouble Shooting


## Closing Thoughts:
* Infrastructure as Code:
* Central RPM repo
* Docker Images
* Uploaded to
     Time and liquid libation 


## References:
Create Local Repo 

     https://wiki.centos.org/HowTos/CreateLocalRepos
     https://www.digitalocean.com/community/tutorials/how-to-set-up-and-use-yum-repositories-on-a-centos-6-vps 
     https://www.ostechnix.com/download-rpm-package-dependencies-centos/

Other Offline Guides
