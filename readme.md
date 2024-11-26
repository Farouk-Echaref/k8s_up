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
