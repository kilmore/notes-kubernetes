# Config Maps

Config Maps bind configuration files, command-line arguments, environment variables, port numbers, and other configuration artifacts to your Pods' containers and system components at runtime. ConfigMaps enable you to separate your configurations from your Pods and components, which helps keep your workloads portable. This makes their configurations easier to change and manage, and prevents hardcoding configuration data to Pod specifications. These are useful for storing and sharing non-sensitive, unencrypted configuration information. 

Config maps passes information into POD's as key value pairs. 

## kubectl commands
Below are common kubectl commands you can run to see/understand deployed config maps

```bash
# Get a list of the config maps
kubectl get config maps

# Describe a config maps
kubectl describe configmaps
```

## Imperative Commands
You can create config maps imperatively. 

**Imperative Example - inline**
Create a config map and provide the config map values inline. 

```bash
kubectl create configmap \
     <config map name> --from-literal=<key name>=<value>

kubectl create configmap \
     app-config --from-literal=APP_COLOR=blue 
```     

**Imperative Example - from file**
Create a config map using a file as the source of the config map data.

```bash
kubectl create configmap \
     <config map name> --from-file=</path/to/file

kubectl create configmap \
     app-config --from-file=app_config.properties
```     
## Config Map via a manifest
The better way create config maps via a manifest file. 


**Simple Config Map**
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
     name: app-config
data:
     APP_COLOR: blue
     APP_MODE: prod
```

## Examples of config maps and pods

**Load a whole config map to a POD**
```yaml
apiVersion: v1
kind: Pod
metadata:
     name: example-pod
spec:
     containers:
     - name: busybox
       image: busbox
       envFrom:            # Instead of defining a specific envar, link the pod
       - configMapRef:     # to a configmap. This imports all values in the configmap
          name: app-config # <-- name of the config map
```

**Use a single value from a config map in an environment variable**
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
               configMapKey
          - name: First_Officer
            valueFrom:
               configMapKeyRef:
                    name: ship-config   # <--- config map we are getting the values from
                    key: FIRST_OFFICER   # <--- Key in the config map we are getting a value from 
```


**Using a config map as a volume mount**
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
       - name: app-config-volume
         configMap:
           name: app-config
```