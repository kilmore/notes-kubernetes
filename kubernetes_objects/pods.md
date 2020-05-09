# PODS

* no naked pods. Always wrapped in an object (statefulset, replica set, deployment, etc.). Naked Pods will not be rescheduled in the event of a node failure.

* Containers should run as not root users
     Use a non-root user inside the container
     When packages are updated inside your container as root, you’ll need to change the user to a non-root user.

     The reason being, if someone hacks into your container and you haven’t changed the user from root, then a simple container escape could give them access to your host where they will be root. When you change the user to non-root, the hacker needs an additional hack attempt to get root access.

     As a best practice, you want as many shells around your infrastructure as possible.

     In Kubernetes you can enforce this by setting the Security context runAsNonRoot: true which will make it a policy-wide setting for the entire cluster.
     
     https://kubernetes.io/docs/tasks/configure-pod-container/security-context/ 

* One process per container  --- You can run more than one process in a container, however it is recommend to run only one single one. This is because of the way the orchestrator works. Kubernetes manages containers based on whether a process is healthy or not. If you have 20 processes running inside a container - how will it know whether its healthy or not?

     To run multiple processes that all talk and depend on one another, you’ll need to run them in Pods.
     
* Log everything to stdout and stderr --- By default Kubernetes listens to these pipes and sends the outputs to your logging service. On Google Cloud for example they go to StackDriver logging automatically.
	
## Best Practices
* No naked podsf. Always wrapped in an object (statefulset, replica set, deployment, etc.). Naked Pods will not be rescheduled in the event of a node failure.

* Containers should run as not root users
     
* One process per container  --- You can run more than one process in a container, however it is recommend to run only one single one. This is because of the way the orchestrator works. Kubernetes manages containers based on whether a process is healthy or not. If you have 20 processes running inside a container - how will it know whether its healthy or not? To run multiple processes that all talk and depend on one another, run as seperate containers in one POD. 

* Log everything to stdout and stderr --- By default Kubernetes listens to these pipes and sends the outputs to your logging service. 

* Don’t use :latest or no tag -- This one is pretty obvious and most people doing this today already. If you don’t add a tag for your container, it will always try to pull the latest one from the repository and that may or may not contain the changes you think it has.