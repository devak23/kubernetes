## Q: How does one change the default namespace in kubernetes
```
kubectl config set-context --current --namespace=ckad
```
## Q: List all the namespaces in a cluster
```
kubectl get namespaces
kubectl get ns
```
## Q: List all pods in a namespace ckad
```
kubectl get pods -n ckad
```
## Q: List all pods in all namespaces
```
kubectl get pods --all-namespaces
```
## Q: List all services in ckad namespace
```
kubectl get svc -n ckad
```
## Q: Create an nginx pod in the default namespace and verify the pod running
```
kubectl run nginx --image=nginx --restart=Never
kubectl get pod nginx
```
## Q: Create a busybox pod and run the command ls while creating it and check the logs
```
kubectl run busybox --image=busybox -- ls
kubectl logs busybox
```
## Q1: Create a new pod named nginx running the image nginx:1.17.10. Expose container port 80. Pod should live in the namespace named ckad (checking: ability to create pod, namespace)
```
kubectl create namespace ckad
kubectl run nginx --image=nginx:1.17.10 --port=80 --namespace=ckad
```

## Q2: Get details of the pod including its ip address (checking: ways to query the pod)
```
kubectl describe pod nginx -n ckad | grep IP
kubectl get pod nginx -n ckad -o wide
```

## Q3: Create a temporary pod that uses busybox image to execute a wget command inside the container. The wget should access the endpoint exposed by nginx container. You should see an HTML response body rendered in a terminal
```
kubectl run busybox --image=busybox --restart=Never --rm -it --namespace=ckad -- wget -O- 172.17.0.3:80
```

