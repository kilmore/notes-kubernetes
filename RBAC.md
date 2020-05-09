To Read:
------------------------------------------------------------------------------------
https://banzaicloud.com/blog/k8s-taints-tolerations-affinities/
https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/
https://docs.bitnami.com/kubernetes/how-to/configure-rbac-in-your-kubernetes-cluster/
https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

https://opencredo.com/drying-out-terraform/
https://anchore.com/blog/docker-security-best-practices-part-1/




------------------------------------------------------------------------------------
https://www.cncf.io/blog/2018/08/01/demystifying-rbac-in-kubernetes/

Key to Understandking RBAC

Three elements of an RBAC
	* Subject - Set of users and processes that want to access to the kubernetes API
	* Resources - Set of K8 API objects available in the cluster. 
		= PODS, deployments, services, Nodes, persistentVolumes, ... 
	* Verbs - set of operations that can be executed to the resources. 
	
The Goal:
	- grant a user (aka subject) the ability to perform operations (verb) on a set of resources (aka... well resources).
	
The mechanism to connect the elements; RBAC API Objects
	* Role - This is an object that binds/links resources and verbs. These are namespace specific. To be cluster wide then the equivelent object is a ClusterRole. 
	
	* RoleBinding - Is the object that links the role to a subject. For cluster level (e.g. non-namespaced equivalent) the object is the cluster role binding 
	
	Role                                   RoleBinding
	----------------------                 ------------------------
	| verb <-> Resources  |    ---->       | Role <-> User         | 
	| (whip the dog)      |                |                       |
	-----------------------                -------------------------