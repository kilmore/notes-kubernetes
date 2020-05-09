# Cluster Admin

When you deploy any infrastructure there is alwasy care and feeding to be done. This set of notes captures "stuff" that you need to do as part of the care and feeding of kubernetes. 


## General Notes (good things to know)

* **Pod Eviction Time Out** - If the Status of the Ready condition remains Unknown or False for longer than the pod-eviction-timeout (an argument passed to the kube-controller-manager), all the Pods on the node are scheduled for deletion by the Node Controller. The default eviction timeout duration is five minutes. This means that if you take a node offline "not gracefully" PODs that are scheduled to it could take up to 5 minutes to be rescheduled to another Node.

* **Drain A Node** - Useful when performing maintenance to ensure POD's are gracefully "moved" (aka rescheduled) from a node to another node. Draining a node marks the node as "unscheduleable", this prevents POD's from being scheduled to it even when it is "back online".

Common options:

  * **--ignore-daemonsets** : if you have defined dameonsets (and they are on the node you are trying to drain) use this option.
  * **--force** : if there are pods running on a node that are not part of a replica set you need to force the eviction. Remember that these POD's will not automatially be re-scheduled. You will need to reschedule them manually... or better yet make them part of a replicaset or stateful set, you know so your app doesn't dis-appear.


```shell
# Reschedule pods on alternate node and cordon's a node
kubectl drain <node name>

# Bring node back online and allow POD's to be scheduled to it
kubectl uncordon <node name>

# Mark a node unscheduleable, does not re-schedule currently running POD's
kubectl cordon <node name>
```




* OS Upgrades
  * [Uprade process](ClusterAdmin/OSUpgrades.md)