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