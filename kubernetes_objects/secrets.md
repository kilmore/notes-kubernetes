# Secrets

* Secrets should be managed vai manifets and tightly couple to the PODs they will be used with. Values for the secrets can be swapped in (token replaced) at build/deployment time. 
* Secrets should be delivered/consumed in the following priority order
     1. The deployed appliction consumes its secrets directly from a service (the Secrets API, valut, etc.). If the application is consuming its secretgs from the kubernetes secrets API the application should be using a Kubernetes service account with RBAC that allows for access to a specific set of secrets.
     2. Environment variables set in the POD definition.
     3. Configuraiton files set in the POD definition.