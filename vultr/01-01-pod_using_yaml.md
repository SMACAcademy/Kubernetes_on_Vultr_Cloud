
- [PODs with YAML](#pods-with-yaml)
- [Create Pod](#create-pod)
- [List Pods](#list-pods)
  - [Create a NodePort Service](#create-a-nodeport-service)
  - [API Object References](#api-object-references)
  - [Updated API Object References](#updated-api-object-references)

# PODs with YAML

Create Simple Pod Definition using YAML 

# Create Pod
kubectl create -f my-pod.yaml
[or]
kubectl apply -f my-pod.yaml

# List Pods
kubectl get pods


## Create a NodePort Service

```
# Create Service
kubectl apply -f my-pod-nodeport.yaml

# List Service
kubectl get svc

# Get Public IP
kubectl get nodes -o wide

# Access Application
http://<WorkerNode-Public-IP>:31231
```

## API Object References
-  **Pod**: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#pod-v1-core
- **Service**: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#service-v1-core

## Updated API Object References
-  **Pod**: https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/pod-v1/
-  **Service**: https://kubernetes.io/docs/reference/kubernetes-api/service-resources/service-v1/
- **Kubernetes API Reference:** https://kubernetes.io/docs/reference/kubernetes-api/

