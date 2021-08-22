# Screenshots
The following screenshots are available in this directory:

## Deployment Pipeline
### DockerHub shows containers pushed
> dockerhub-images.png
  
### Integration between Travis and Github
> travis-github-integration.png

### Travis CI successful build and deploy job
> successful-travis-build.png

## Kubernetes

### Shows running pods and udagram-api-user has 2 replicas running:
```bash
kubectl get pods
```
> udagram-api-user-has-2-replicas.png


### Shows Kubernetes services
```bash
kubectl describe services
```
> kubectl-describe-services.png,
> kubectl-get-services.png

### Shows horizontal scaling set against CPU usage
```bash
kubectl describe hpa
```
> kubectl-hpa-describe.png, 
> kubectl-hpa-config.png

### Shows logging of udagram-api-feed
```bash
kubectl logs <pod-name>
```
> kubectl-logs-pod-1.png,
> kubectl-logs-pod-2.png

### Shows front-end accessing application in Kubernetes
> front-end-running-with-k8s.png


