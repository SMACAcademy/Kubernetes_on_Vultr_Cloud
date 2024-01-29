# 1. Kubernetes Basic Commands

List of basic commands for Kubernetes management:

- [1. Kubernetes Basic Commands](#1-kubernetes-basic-commands)
  - [1.1. PODs Demo](#11-pods-demo)
    - [1.1.1. Get Worker Nodes Status](#111-get-worker-nodes-status)
    - [1.1.2. Create a Pod](#112-create-a-pod)
    - [1.1.3. List Pods](#113-list-pods)
    - [1.1.4. List Pods with wide option](#114-list-pods-with-wide-option)
    - [1.1.5. Describe Pod](#115-describe-pod)
    - [1.1.6. Access Application](#116-access-application)
    - [1.1.7. Delete Pod](#117-delete-pod)
  - [1.2. Demo - Expose Pod with a Service](#12-demo---expose-pod-with-a-service)
  - [1.3. Interact with a Pod](#13-interact-with-a-pod)
    - [1.3.1. Verify Pod Logs](#131-verify-pod-logs)
    - [1.3.2. Connect to Container in a POD](#132-connect-to-container-in-a-pod)
  - [1.4.  Get YAML Output of Pod \& Service](#14--get-yaml-output-of-pod--service)
  - [1.5. Clean-Up](#15-clean-up)


## 1.1. PODs Demo
### 1.1.1. Get Worker Nodes Status

```
# Get Worker Node Status
kubectl get nodes

# Get Worker Node Status with wide option
kubectl get nodes -o wide
```

### 1.1.2. Create a Pod
- Create a Pod
```
# Template
kubectl run <desired-pod-name> --image <Container-Image> 

# Replace Pod Name, Container Image
kubectl run my-first-alpha --image nginx  
```  

### 1.1.3. List Pods
- Get the list of pods
```
# List Pods
kubectl get pods

# Alias name for pods is po
kubectl get po
```

### 1.1.4. List Pods with wide option
- List pods with wide option which also provide Node information on which Pod is running
```
kubectl get pods -o wide
```

### 1.1.5. Describe Pod

```
# To get list of pod names
kubectl get pods

# Describe the Pod
kubectl describe pod <Pod-Name>
kubectl describe pod my-first-alpha 
```

### 1.1.6. Access Application
- Currently we can access this application only inside worker nodes. 
- To access it externally, we need to create a **NodePort Service**. 

### 1.1.7. Delete Pod
```
# To get list of pod names
kubectl get pods

# Delete Pod
kubectl delete pod <Pod-Name>
kubectl delete pod my-first-alpha
```

## 1.2. Demo - Expose Pod with a Service
- Expose pod with a service (NodePort Service) to access the application externally (from internet)
- **Ports**
  - **port:** Port on which node port service listens in Kubernetes cluster internally
  - **targetPort:** We define container port here on which our application is running.
  - **NodePort:** Worker Node port on which we can access our application.
```
# Create  a Pod
kubectl run my-first-alpha --image nginx

# Expose Pod as a Service
kubectl expose pod my-first-alpha  --type=NodePort --port=80 --name=my-service-alpha

# Get Service Info
kubectl get service
kubectl get svc

# Get Public IP of Worker Nodes
kubectl get nodes -o wide
```
- **Access the Application using Public IP**
```
http://<node1-public-ip>:<Node-Port>
```

```
# Below command will fail when accessing the application, as service port (85) and container port (80) are different
kubectl expose pod my-first-alpha  --type=NodePort --port=85 --name=my-service-alpha2     

# Expose Pod as a Service with Container Port (--taret-port)
kubectl expose pod my-first-alpha  --type=NodePort --port=81 --target-port=80 --name=my-service-alpha3

# Get Service Info
kubectl get service
kubectl get svc

# Get Public IP of Worker Nodes
kubectl get nodes -o wide
```
- **Access the Application using Public IP**
```
http://<node1-public-ip>:<Node-Port>
```

## 1.3. Interact with a Pod

### 1.3.1. Verify Pod Logs
```
# Get Pod Name
kubectl get po

# Dump Pod logs
kubectl logs my-first-alpha

# Stream pod logs with -f option and access application to see logs
kubectl logs -f my-first-alpha
```
- **Important Notes**
  - **Reference:** https://kubernetes.io/docs/reference/kubectl/cheatsheet/

### 1.3.2. Connect to Container in a POD
- **Connect to a Container in POD and execute commands**
```
# Connect to Nginx Container in a POD
kubectl exec -it my-first-alpha -- /bin/bash

# Execute some commands in Nginx container
ls
cd /usr/share/nginx/html
cat index.html
exit
```

- **Running individual commands in a Container**
```
kubectl exec -it <pod-name> env

# Sample Commands
kubectl exec -it my-first-alpha env
kubectl exec -it my-first-alpha ls
kubectl exec -it my-first-alpha cat /usr/share/nginx/html/index.html
```
## 1.4.  Get YAML Output of Pod & Service

```
# Get pod definition YAML output
kubectl get pod my-first-alpha -o yaml   

# Get service definition YAML output
kubectl get service my-service-alpha -o yaml   
```

## 1.5. Clean-Up
```
# Get all Objects in default namespace
kubectl get all

# Delete Services
kubectl delete svc my-service-alpha
kubectl delete svc my-service-alpha2
kubectl delete svc my-service-alpha3

# Delete Pod
kubectl delete pod my-first-alpha

# Get all Objects in default namespace
kubectl get all
```