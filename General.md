Certified Kubernetes Administrator: https://www.cncf.io/certification/cka/

Exam Curriculum (Topics): https://github.com/cncf/curriculum

Candidate Handbook: https://www.cncf.io/certification/candidate-handbook

Exam Tips: http://training.linuxfoundation.org/go//Important-Tips-CKA-CKAD



Use the code - DEVOPS15 - while registering for the CKA or CKAD exams at Linux Foundation to get a 15% discount.


## Control Plane Components
EtcD      (database for kubernetes cluster)
Scheduler (kube-scheduler)
Controller Manager 
     * Node Controller
     * Replication Controller
API Server   (kube-apiserver)

## Conatiner Run Time
Supported Conatiner Run Times
* Docker
* ContainerD
* RKT

## Work Node Components
Kubelet 
Kube-Proxy -- Ensures that the necessary rules to allow the containers running on the worker node to reach each other 


kube-api server is the primary management component in kubernetes
     - configuration is stored
       - KubeADM (aka running as a container) ->/etc/kubernetes/manifests/kube-apiserver.yaml
       - Installed as a service -> /etc/systemd/system/kube-apiserver.servce
     - Can see the effective options on the running node by
       - `ps -aux | grep kube-apiserver`

Controller-Manager
     - watch various components of the cluster - Remediate issues
       - pod/container health
       - name spaces (e.g. namespace controller)
       - nodes (e.g. node-controller)
       - replica sets (e.g. replication-controller)
     - installed as part of the kube-controller manager
     - congfigurations are store
       - kubeadm -> /etc/kubernetes/manifests/kube-controller-manager.yaml
       - installed as a service -> /etc/systemd/system/kube-controller-manager.service
     - Can see the effective options on the running node by
       - `ps -aux | grep kube-controller-manager`
  
Kube-Scheduler
     - responsible for deciding which nodes get which pods, it does not do the creation
     - Two pass
       - Filter 
       - Rank remaining nodes - calculates the amount of resources after placing the pod. The system that has more resources after the pod is scheduled gets a higher rank
     - Other impacs
       - Labels, resource limits, taints and tolerations
     - congfigurations are store
       - kubeadm -> /etc/kubernetes/manifests/kube-scheduler.yaml
       - installed as a service -> /etc/systemd/system/kube-scheduler.service
     - Can see the effective options on the running node by
       - `ps -aux | grep kube-scheduler`
  
  Kublet
     - Registers node with master
     - Monitors node and pods for the node
     - kubeadm doesn't deploy kublet


  Kube-proxy
     - Updates ip tables rule on each node in the cluster to forward traffic to the ip of the target pod
     - when using kubeadm kube-proxy is deployed as a daemon set on each node in the cluster


Manifests all have 4 root level elements
- apiVersion:
- kind
- metadata
- spec



## Create sample YAML when you can't remember the YAML
Create an NGINX Pod
`kubectl run --generator=run-pod/v1 nginx --image=nginx`

Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)
`kubectl run --generator=run-pod/v1 nginx --image=nginx --dry-run -o yaml`

Create a deployment
`kubectl create deployment --image=nginx nginx`

Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)
`kubectl create deployment --image=nginx nginx --dry-run -o yaml`

Generate Deployment YAML file (-o yaml). Don't create it(--dry-run) with 4 Replicas (--replicas=4)
`kubectl create deployment --image=nginx nginx --dry-run -o yaml > nginx-deployment.yaml`



## taints and tolerations
* Taint: Applied to nodes
* Tolerations: Applied to pods
* By default pods are not scheduled onto nodes with taints.
* Tolerations can be added to pods to tolerate taints.
* Taint-Effects
  * NoSchedule - Pod's will not be scheduled
  * PreferNoSchedule - Try to avoid, but not guaranteed
  * NoExecute - No new nodes will be added, and existing pods will be evicted

```
  kubectl taint nodes node-name key=value:taint-effect
  kubectl taint nodes node1 app=blue:NoSchedule
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
  - name: nginx-container
    image: nginx
  
  tolerations:
  - Key: "app"
    operator: "Equal"
    value: "blue"
    effect: "NoSchedule"
```
