### Q: How does one change the default namespace in kubernetes
```
kubectl config set-context --current --namespace=ckad
```
### Q: List all the namespaces in a cluster
```
kubectl get namespaces
kubectl get ns
```
### Q: List all pods in a namespace ckad
```
kubectl get pods -n ckad
```
### Q: List all pods in all namespaces
```
kubectl get pods --all-namespaces
```
### Q: List all services in ckad namespace
```
kubectl get svc -n ckad
```
### Q: Create an nginx pod in the default namespace and verify the pod running
```
kubectl run nginx --image=nginx --restart=Never
kubectl get pod nginx
```
### Q: Create a busybox pod and run the command ls while creating it and check the logs
```
kubectl run busybox --image=busybox -- ls
kubectl logs busybox
```
### Q1: Create a new pod named nginx running the image nginx:1.17.10. Expose container port 80. Pod should live in the namespace named ckad
```
kubectl create namespace ckad
kubectl run nginx --image=nginx:1.17.10 --port=80 --namespace=ckad
```

### Q2: Get details of the pod including its ip address
```
kubectl describe pod nginx -n ckad | grep IP
kubectl get pod nginx -n ckad -o wide
```

### Q3: Create a temporary pod that uses busybox image to execute a wget command inside the container. The wget should access the endpoint exposed by nginx container. You should see an HTML response body rendered in a terminal
```
kubectl run busybox --image=busybox --restart=Never --rm -it --namespace=ckad -- wget -O- 172.17.0.3:80
```
In this case though, set the hostNetwork parameter to true in the spec section. Only then you will be able to access the pod's end point. Ofcourse there are other ways.

### Q4: Get logs of nginx container
```
kubectl logs nginx
```

### Q5: Add env variable DB_URL=postgresql://mydb:5432 and DB_USER_NAME=admin to the nginx container pod
Editing a live pod is forbidden. The only way to do this is to delete the existing pod. Update the manifest adding the env entries in the containers section and re-creating the pod.

### Q6: Open a shell for nginx container and inspect the contents of the current directory
```
kubectl exec -it -- /bin/bash
```