# OS Upgrades

At somepoint you will need to upgrade the OS, apply patches, update the kubectl... you know "do stuff" to a node. My prefered option is to not do an implace upgrade (altho sometimes you have to). It is generally better to replace an existing node with a new node. In the modern age [immutable infrastructure](https://www.hashicorp.com/resources/what-is-mutable-vs-immutable-infrastructure/) is really the way to go. 


## Proccess - worker node

1. Rescheduled naked PODs to other nodes, and [re-think](https://kubernetes.io/docs/concepts/configuration/overview/#naked-pods-vs-replicasets-deployments-and-jobs) your approach on using naked PODs in the first place. 
2. Drain PODs
3. Remove node from cluster
4. Add new node to cluster, with same taints, tolerations, labels, etc.
5. Schedule a pod to the new node to ensure it works (or hey run your end to end integration tests.... you have them right?)
6. Repeat until done.
7. Have a beer and hang out for the inevitable support call.
