- [Create ReplicaSet](#create-replicaset)
  - [Create ReplicaSet](#create-replicaset-1)
  - [List ReplicaSets](#list-replicasets)
  - [Describe ReplicaSet](#describe-replicaset)
  - [List of Pods](#list-of-pods)
  - [Verify the Owner of the Pod](#verify-the-owner-of-the-pod)
- [Expose ReplicaSet as a Service](#expose-replicaset-as-a-service)
- [Test Replicaset Reliability or High Availability](#test-replicaset-reliability-or-high-availability)
- [Test ReplicaSet Scalability feature](#test-replicaset-scalability-feature)
- [Delete ReplicaSet \& Service](#delete-replicaset--service)
  - [Delete ReplicaSet](#delete-replicaset)
  - [Delete Service created for ReplicaSet](#delete-service-created-for-replicaset)



## Create ReplicaSet

### Create ReplicaSet
- Create ReplicaSet
```
kubectl create -f replicaset-demo.yml
```

### List ReplicaSets
- Get list of ReplicaSets
```
kubectl get replicaset
kubectl get rs
```

### Describe ReplicaSet
- Describe the newly created ReplicaSet
```

kubectl describe replicaset my-helloworld-rs

kubectl describe rs my-helloworld-rs
```

### List of Pods
- Get list of Pods
```
#Get list of Pods
kubectl get pods


# Get list of Pods with Pod IP and Node in which it is running
kubectl get pods -o wide
```

### Verify the Owner of the Pod
- Verify the owner reference of the pod under **"name"** tag under **"ownerReferences"**.  
```
kubectl get pods <pod-name> -o yaml
kubectl get pods my-helloworld-rs-xxxx -o yaml 
```

## Expose ReplicaSet as a Service
- Expose ReplicaSet with a service (NodePort Service) to access the application externally (from internet)
```
# Expose ReplicaSet as a Service

kubectl expose rs my-helloworld-rs  --type=NodePort --port=80 --target-port=8080 --name=my-helloworld-rs-service

# Get Service Info
kubectl get service
kubectl get svc

# Get Public IP of Worker Nodes
kubectl get nodes -o wide
```
- **Access the Application using Public IP**
```
http://<node1-public-ip>:<Node-Port>/hello
```

## Test Replicaset Reliability or High Availability 
- Test how the high availability or reliability concept is achieved automatically in Kubernetes
- Whenever a POD is accidentally terminated due to some application issue, ReplicaSet should auto-create that Pod to maintain desired number of Replicas configured to achive High Availability.
```
# To get Pod Name
kubectl get pods

# Delete the Pod
kubectl delete pod <Pod-Name>

# Verify the new pod got created automatically
kubectl get pods   
``` 

## Test ReplicaSet Scalability feature 
- Test how scalability is going to seamless & quick
- Update the **replicas** field in **replicaset-demo.yml** from 3 to 6.
```
# Apply latest changes to ReplicaSet
kubectl replace -f replicaset-demo.yml

# Verify if new pods got created
kubectl get pods -o wide
```

## Delete ReplicaSet & Service
### Delete ReplicaSet
```
# Delete ReplicaSet
kubectl delete rs <ReplicaSet-Name>

# Sample Commands
kubectl delete replicaset my-helloworld-rs
[or]
kubectl delete rs my-helloworld-rs

# Verify if ReplicaSet got deleted
kubectl get rs
```

### Delete Service created for ReplicaSet
```
# Delete Service
kubectl delete svc <service-name>

# Sample Commands
kubectl delete svc my-helloworld-rs-service
[or]
kubectl delete svc/my-helloworld-rs-service

# Verify if Service got deleted
kubectl get svc
```


