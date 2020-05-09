# Secrets

A Secret is an object that contains a small amount of sensitive data such as a password, a token, or a key. Such information might otherwise be put in a Pod specification or in an image. Users can create secrets and the system also creates some secrets. To use a secret, a Pod needs to reference the secret. A secret can be used with a Pod in two ways:
     1. As files in a volume mounted on one or more of its containers.
     2. By the kubelet when pulling images for the Pod.


Important things to note:

* A secret is only sent to a node if a pod on that node requires it.
* Kubelet stores the secret into a tmpfs so that the secret is not written to disk storage.
* Once the Pod that depends on the secret is deleted, kubelet will delete its local copy of the secret data as well


Secrets encode data in base64 format. Anyone who captures a base64 encoded secret can easily decode it. Additional steps need to be taken to ensure that secrets remain... secret:
1. Do not store secrets in SCM
2. Encrypt the data at Rest if stored in etcd [link](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/)
3. Understand protection options [link](https://kubernetes.io/docs/concepts/configuration/secret/#protections)
4. Secure against risks [link](https://kubernetes.io/docs/concepts/configuration/secret/#risks)
5. Develop containers such that there is [partitioning at pod level](https://kubernetes.io/docs/concepts/configuration/secret/#use-case-secret-visible-to-one-container-in-a-pod)
6. Use a 3rd part tool like Vault to store secrets


## kubectl commands
Below are common kubectl commands you can run to see/understand deployed config maps

```bash
# Get a list of the config maps
kubectl get secrets

# Describe a config maps
kubectl describe secrets

# Base 64 Encode your secrets....
echo -n <your password here> | base64

# Bae 64 De-Code your secret
echo -n <your encoded password> | base64 --decode

```

## Imperative Commands
You can create config maps imperatively. 

**Imperative Example - inline**
Create a config map and provide the config map values inline. 

```bash
kubectl create secret generic\
     <secret-name> --from-literal=<key>=<value>

kubectl create secret generic \
     app-secrets --from-literal=DB_PASSWORD=MySecretPassword
```     

**Imperative Example - from file**
Create a config map using a file as the source of the config map data.

```bash
kubectl create secret generic \
     <secret-name> --from-file=</path/to/file>

kubectl create configmap \
     app-secrets --from-file=app_access.properties
```     

## Secret via a manifest
The better way create config maps via a manifest file. The secret values must be provided in a base 64 encoded format. 



**Simple Secret**
```yaml
apiVersion: v1
kind: Secret
metadata:
     name: app-secret
data:
     DB_Password: TXlTZWNyZXRQYXNzd29yZA==  # <--- Base64 encoded MySecretPassword
```


## Examples of secrets and pods

**Load a whole Secret to a POD**
```yaml
apiVersion: v1
kind: Pod
metadata:
     name: example-pod
spec:
     containers:
     - name: busybox
       image: busbox
       envFrom:            # Instead of defining a specific envar that is a secret, link the pod
       - secretRef:        # to a secret. This imports all values in the secret
         valueFrom:
           name: app-secrets # <-- name of the secret object
```

**Use a single value from a secret in an environment variable**
```yaml
apiVersion: v1
kind: Pod
metadata:
     name: example-pod
spec:
     containers:
     - name: busybox
       image: busbox
       env:
          - name: Captain
            valueFrom: 
               secretKeyRef
          - name: SELF_DESTRUCT_PASSWORD
            valueFrom:
               configMapKeyRef:
                    name: ship-secrets            # <--- secret we are getting the values from
                    key: FIRST_OFFICER_PASSWORD   # <--- Key in the secret we are getting a value from
```


**Using a secret as a volume mount**
Each secret is mounted as an indivdual file, with the key name as the file name and the vaule in the file

```yaml
apiVersion: v1
kind: Pod
metadata:
     name: example-pod
spec:
     containers:
     - name: busybox
       image: busbox
       volumes:
       - name: secret-volume
         configMap:
           name: ship-secrets    # <--- The secret map
```