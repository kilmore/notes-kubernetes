
Sometimes you just want to use a specific namespace
```
# First get your context name
$ kubectl config get-context
your-awesome-cluster

# Now set your namespace
$ kubectl config set-context your-awesome-cluster --namespace=stellar
Context "your-awesome-cluster" modified.

```

Sometimes you just need a shell in cluster
```
kubectl run myshell --rm -i --tty --image ubuntu -- bash
```

Sometimes you just to get a shell on one of the PODS in the cluster
```
kubectl exec -it <target pod> -- /bin/bash
```

kubeadm token create --print-join-command

WebUI dashboard
kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml

accessing 
kubectl port-forward svc/kubernetes-dashboard 8080:80 --namespace=kube-system