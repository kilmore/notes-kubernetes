
## Cluster Introspection
kubectl get services                # List all services 
kubectl get pods                    # List all pods
kubectl get nodes -w                # Watch nodes continuously
kubectl version                     # Get version information
kubectl cluster-info                # Get cluster information
kubectl config view                 # Get the configuration
kubectl describe node <node>        # Output information about a node



## Pod and Container Introspection
kubectl get pods                         # List the current pods
kubectl describe pod <name>              # Describe pod <name>
kubectl get rc                           # List the replication controllers
kubectl get rc --namespace="<namespace>" # List the replication controllers in <namespace>
kubectl describe rc <name>               # Describe replication controller <name>
kubectl get svc                          # List the services
kubectl describe svc <name>              # Describe service <name>

## Interacting with Pods
kubectl run <name> --image=<image-name>                             # Launch a pod called <name> 
                                                                    # using image <image-name>

kubectl create -f <manifest.yaml>                                   # Create a service described 
                                                                    # in <manifest.yaml>

kubectl scale --replicas=<count> rc <name>                          # Scale replication controller 
                                                                    # <name> to <count> instances

kubectl expose rc <name> --port=<external> --target-port=<internal> # Map port <external> to 
                                                                    # port <internal> on replication 
                                                                    # controller <name>

kubectl cp <file-spec-src> <file-spec-dest> -c <specific-container>  # copy a file from a pod and/or container
                                                                     # ONLY DO THIS FOR DEBUGINGG PORPOSES. 


## Stopping Kubernetes
kubectl delete pod <name>                                         # Delete pod <name>
kubectl delete rc <name>                                          # Delete replication controller <name>
kubectl delete svc <name>                                         # Delete service <name>
kubectl drain <n> --delete-local-data --force --ignore-daemonsets # Stop all pods on <n>
kubectl delete node <name>                                        # Remove <node> from the cluster

## Debugging
kubectl exec <service> <command> [-c <$container>] # execute <command> on <service>, optionally 
                                                   # selecting container <$container>

kubectl logs -f <name> [-c <$container>]           # Get logs from service <name>, optionally
                                                   # selecting container <$container>

watch -n 2 cat /var/log/kublet.log                 # Watch the Kublet logs
kubectl top node                                   # Show metrics for nodes
kubectl top pod                                    # Show metrics for pods
journalctl -u kubelet

kubectl port-forward <pod name> <local port>:<remote port>   # Like SSH port forwarding. Allows you to access exposed ports on the POD via localhost       

kubectl --insecure-skip-tls-verify                 # Useful for when you are connecting to a cluster not by the name used to generate the certs