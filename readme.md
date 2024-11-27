# K8S Up & Running

## Chap2(Docker):
* for docker resources:
- focus on building, caching and layer dependcy
- https://docs.docker.com/get-started/docker-concepts/building-images/
- image security
- multistage image builds
- Limiting Resource Usage:
```bash
$ docker run -d --name kuard \ --publish 8080:8080 \
--memory 200m \
--memory-swap 1G \
--cpu-shares 1024 \ gcr.io/kuar-demo/kuard-amd64:blue
```

## Chap3(K8s cluster):
- check if your cluster is healthy:
```bash
kubectl get componentstatus

# ==> output
NAME                 STATUS    MESSAGE   ERROR
controller-manager   Healthy   ok        
scheduler            Healthy   ok        
etcd-0               Healthy   ok   
```

## Chap4(Kubectl commands):

- get more detailed output:

```bash
kubectl get all -o wide
kubectl get all -o json #json format output
kubectl get all -0 yanl #yaml format output
```

- extracting specific informations (using JSONPath query):

```bash
# extract the the IP address of a specific POD
kubectl get pods pod-name -o jsonpath --template={.status.podIP}
```

- for debugging purposes you can check the logs of the running pods (containers):

```bash
kubectl logs pod-name # add -f to keep watching the logs live
```

- execute commands in a pod:

```bash
kubectl exec -it pod-name -- bash
```

- check resources:

```bash
kubectl top nodes
kubectl top pods
```

## Chap5(Pods):

- port forwarding:

```bash
kubectl port-forward pod-name 8080:8080
```

- liveness probe (go back to)/readiness probe:
* https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/

- Resource management:
* requested resources vs limit resources :
```yaml
resources:
    requests:
        cpu: "500m"
        memory: "128Mi"
    limits:
        cpu: "1000m"
        memory: "256Mi"
```

- volume persisting:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kuard
spec:
  volumes:
    - name: kuard-data
      hostPath:
        path: "/var/lib/kuard"
  containers:
    - image: gcr.io/kuar-demo/kuard-amd64:blue
      name: kuard
      volumeMounts:
        - mountPath: "/data"
          name: kuard-data
      ports:
        - containerPort: 8080
          name: http
          protocol: TCP
```

- using remote disks(NFS (Network File System)):

```yaml
volumes:
  - name: kuard-data
    nfs:
      server: my.nfs.server.local
      path: "/exports"
```
### 1. Pod with HostPath Volume

#### Description:
- This example defines a Pod with a single container (`kuard`). The container uses a `hostPath` volume to access a specific directory on the host machine.

#### Key Points:
- **Volume Type:** `hostPath`
- **Host Directory:** `/var/lib/kuard`
- **Container Mount Path:** `/data`
- **Exposed Port:** `8080` (HTTP, TCP protocol)

#### Use Case:
- Enables the container to persist data on the host machine or access host-specific files.

---

### 2. Volume with NFS

#### Description:
- This example defines an NFS volume that can be shared across multiple containers or Pods. The volume connects to a remote NFS server.

#### Key Points:
- **Volume Type:** `NFS`
- **NFS Server:** `my.nfs.server.local`
- **Exported Path:** `/exports`
- **Volume Name:** `kuard-data`

#### Use Case:
- Facilitates shared storage between containers and Pods, with data stored on a centralized NFS server.

### Wrap up Pods:

```yaml
apiVersion: v1  # Specifies the API version for the Pod resource.
kind: Pod       # Defines the kind of Kubernetes resource (Pod in this case).
metadata:
  name: kuard  # Sets the name of the Pod to "kuard".
spec:
  volumes:  # Defines the volumes available to the Pod.
    - name: kuard-data  # The name of the volume to be referenced in containers.
      nfs:  # Configures an NFS volume.
        server: my.nfs.server.local  # The hostname or IP of the NFS server.
        path: "/exports"  # The path on the NFS server to be mounted.

  containers:  # Defines the containers within the Pod.
    - image: gcr.io/kuar-demo/kuard-amd64:blue  # Specifies the container image.
      name: kuard  # Assigns the name "kuard" to the container.
      ports:  # Lists ports exposed by the container.
        - containerPort: 8080  # The port exposed by the container.
          name: http  # Names the port for use in services or probes.
          protocol: TCP  # Specifies the protocol (TCP).

      resources:  # Defines resource requests and limits for the container.
        requests:  # Minimum resources required for the container.
          cpu: "500m"  # Requests 500 millicores of CPU.
          memory: "128Mi"  # Requests 128 MiB of memory.
        limits:  # Maximum resources the container can use.
          cpu: "1000m"  # Limits CPU usage to 1 core (1000 millicores).
          memory: "256Mi"  # Limits memory usage to 256 MiB.

      volumeMounts:  # Mounts the defined volume into the container.
        - mountPath: "/data"  # The path in the container where the volume is mounted.
          name: kuard-data  # References the volume "kuard-data".

      livenessProbe:  # Checks if the container is still running.
        httpGet:
          path: /healthy  # The HTTP endpoint used to check health.
          port: 8080  # The port on which the probe is performed.
        initialDelaySeconds: 5  # Waits 5 seconds before the first probe.
        timeoutSeconds: 1  # Waits up to 1 second for a response.
        periodSeconds: 10  # Probes every 10 seconds.
        failureThreshold: 3  # Marks the container as unhealthy after 3 failures.

      readinessProbe:  # Checks if the container is ready to serve traffic.
        httpGet:
          path: /ready  # The HTTP endpoint used to check readiness.
          port: 8080  # The port on which the probe is performed.
        initialDelaySeconds: 30  # Waits 30 seconds before the first probe.
        timeoutSeconds: 1  # Waits up to 1 second for a response.

```






