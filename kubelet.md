# Kubelet


## Static Pods

Default Location
- /etc/Kuberenetes/manifests

kubeconfig.yaml
     staticPodPath:

kubelet.service
     --pod-manifest-path= <path to where the static pods are stored>
     or
     --kubeconfig= <path to the kubelet config>