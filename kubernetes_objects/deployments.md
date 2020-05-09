# Deployments

* Use the “record” option for easier rollbacks 

Label it up 

Don’t use :latest or no tag -- This one is pretty obvious and most people doing this today already. If you don’t add a tag for your container, it will always try to pull the latest one from the repository and that may or may not contain the changes you think it has.


* Readiness and Liveness Probes are your friend
     Probes can be used so that Kubernetes knows if nodes are healthy and if it should send traffic to it. By default Kubernetes checks if processes are running or not running. But by using probes you can leverage this default behaviour in Kubernetes to add your own logic.

* Services -- use node-port or ingress

* Map External Services to Internal Ones -- This is something that most people don’t know you can do in Kubernetes. If you need a service that is external to the cluster, what you can do is use a service with the type ExternalName. Now you can just call the service by its name and the Kubernetes manager passes you on to it as if it’s part of the cluster. Kubernetes treats the service as is if it is on the same network, but it sits actually outside of it.
	

* Use the “record” option for easier rollbacks 
* Label it up 

* Readiness and Liveness Probes are your friend
     Probes can be used so that Kubernetes knows if nodes are healthy and if it should send traffic to it. By default Kubernetes checks if processes are running or not running. But by using probes you can leverage this default behaviour in Kubernetes to add your own logic.

**Services** 
     * Use node-port or ingress 
     * Create before the backend workloads (e.g. deployment or ReplicatSets) and before any workloads that need to access it. 
     * Don't use environment variables to point to a service, when possible. Use DNS lookups as the default mechanism.
     * Use a service and a nodeport for the service over exposing a hostport on a POD
     * Map external services to internal ones using service registration. 

**Labels**
     Define and use labels that identify semantic attributes of your application or Deployment, such as  app: myapp, tier: frontend, phase: test, deployment: v3 }