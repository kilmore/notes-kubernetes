# PODs


## Container Arguments

Container arguments are equivalent to passing a command to a POD at Docker run time. The arguments are provided to the container at POD startup. Remember, that the arguments override any default command set in the container. 
Arguments are set in the container specification an provided as an array.

**Example**
```yaml

apiVersion: v1
kind: Pod
metadata:
     name: ubuntu-sleeper-pod
spec:
     containers:
     - name: busybox
       image: busbox
       args: ["arg1","arg2"]   # <---- Arguments.
```

## Container Commannds
Container arguments are equivalent to using the *--entrypoint* flag at container run time.  Remember, that this overrides the entrypoint directive set in the container. 
**Example**
```yaml

apiVersion: v1
kind: Pod
metadata:
     name: ubuntu-sleeper-pod
spec:
     containers:
     - name: busybox
       image: busbox
       command: ["entrypoint.sh"]  # <---- Command
       args: ["arg1","arg2"]   # <---- Arguments
```

## Environment Variables
This is an array of key value pairs (e.g. think of a map). The name is the name of the environment variable being supplied to the container, and the value is the value being set as the environment variable. 

Environment variables can also be set via config maps and secrets. Similar syntax, howver the value of the environment variable comes from the valueFrom directive.

**Example - simple Environment Variable**
```yaml
apiVersion: v1
kind: Pod
metadata:
     name: ubuntu-sleeper-pod
spec:
     containers:
     - name: busybox
       image: busbox
       env:
          - name: Captain 
            value: Kirk
          - name: First_Office
            value: Mr. Spock
```

**Example -Config Map**
```yaml
apiVersion: v1
kind: Pod
metadata:
     name: ubuntu-sleeper-pod
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

**Example -Secret**
```yaml
apiVersion: v1
kind: Pod
metadata:
     name: ubuntu-sleeper-pod
spec:
     containers:
     - name: busybox
       image: busbox
       env:
          - name: Omega_Password
            valueFrom:
               secretKeyRef:
```


## Multi-Container Patterns
* Side Car
* adapter 
* ambassador


## Init Containers
When a POD is first created the initContainer is run, and the process in the initContainer must run to a completion before the real container hosting the application starts. 

If any of the initContainers fail to complete, Kubernetes restarts the Pod repeatedly until the Init Container succeeds.

[Official docs](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)

**Example - simple**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox
    command: ['sh', '-c', 'git clone <some-repository-that-will-be-used-by-application> ; done;']
```

**Example - Multiple Init Containers**
Multiple such initContainers can be configured in a POD. In this case each init container is run one at a time in sequential order.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox:1.28
    command: ['sh', '-c', 'until nslookup myservice; do echo waiting for myservice; sleep 2; done;']
  - name: init-mydb
    image: busybox:1.28
    command: ['sh', '-c', 'until nslookup mydb; do echo waiting for mydb; sleep 2; done;']
```


## Livness and Readiness Probes
