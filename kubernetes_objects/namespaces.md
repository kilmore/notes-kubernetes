# Namespaces

set the namespace in the install command 

Namespaces are the way to partition a single Kubernetes cluster into multiple virtual clusters. Each user, team of users, or application may exist within its Namespace, isolated from every other user of the cluster and operating as if it were the sole user of the cluster.

Software development teams customarily partition their development pipelines into discrete units. These units take various forms and use various labels but will tend to result in a discrete dev environment, a testing|QA environment, possibly a staging environment and finally a production environment. The resulting layouts are ideally suited to Kubernetes Namespaces. Each environment or stage in the pipeline becomes a unique namespace.

The above works well as each namespace can be templated and mirrored to the next subsequent environment in the dev cycle, e.g. dev->qa->prod. The fact that each namespace is logically discrete allows the development teams to work within an isolated “development” namespace. DevOps (The closest role at Google is called Site Reliability Engineering “SRE”)  will be responsible for migrating code through the pipelines and ensuring that appropriate teams are assigned to each environment. Ultimately, DevOps is solely responsible for the final, production environment where the solution is delivered to the end-users.

A major benefit of applying namespaces to the development cycle is that the naming of software components (e.g. micro-services/endpoints) can be maintained without collision across the different environments. This is due to the isolation of the Kubernetes namespaces, e.g. serviceX in dev would be referred to as such across all the other namespaces; but, if necessary, could be uniquely referenced using its full qualified name serviceX.development.mycluster.com in the development namespace of mycluster.com.

Anti-patterns

Abusing the namespace benefit resulting in unnecessary environments in the development pipeline. So; if you don’t do staging deployments, don’t create a “staging” namespace.
Overcrowding namespaces e.g. having all your development projects in one huge “development” namespace. Since namespaces attempt to partition, use these to partition by your projects as well. Since Namespaces are flat, you may wish something similar to: projectA-dev, projectA-prod as projectA’s namespaces.


<Service Aame>.<Namespace Name>.svc.cluster.local

## Best Practices
**Patterns**
Namespaces are the way to partition a single Kubernetes cluster into multiple virtual clusters. Each user, team of users, or application may exist within its Namespace, isolated from every other user of the cluster and operating as if it were the sole user of the cluster.

* Namespace based on environment. For example; Development, QA, Production. 
* Namespace based on a cross cutting set of needs; monitoring, centralized logging, etc. 

**Anti-patterns:**
* Abusing the namespace benefit resulting in unnecessary environments in the development pipeline. So; if you don’t do staging deployments, don’t create a “staging” namespace. 
* Overcrowding namespaces e.g. having all your development projects in one huge “development” namespace. Since namespaces attempt to partition, use these to partition by your projects as well. Since Namespaces are flat, you may wish something similar to: projectA-dev, projectA-prod as projectA’s namespaces.