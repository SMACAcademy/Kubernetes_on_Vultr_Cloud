- [Kubernetes - Deployment](#kubernetes---deployment)
  - [Create Deployment](#create-deployment)
  - [Scaling a Deployment](#scaling-a-deployment)
  - [Expose Deployment as a Service](#expose-deployment-as-a-service)
- [Kubernetes - Update Deployments](#kubernetes---update-deployments)
  - [Updating Application version V1 to V2 using "Set Image" Option](#updating-application-version-v1-to-v2-using-set-image-option)
    - [Update Deployment](#update-deployment)
    - [Verify Rollout Status (Deployment Status)](#verify-rollout-status-deployment-status)
    - [Describe Deployment](#describe-deployment)
    - [Verify ReplicaSet](#verify-replicaset)
    - [Verify Pods](#verify-pods)
    - [Verify Rollout History of a Deployment](#verify-rollout-history-of-a-deployment)
    - [Access the Application using Public IP](#access-the-application-using-public-ip)
  - [Update the Application from V2 to V3 using "Edit Deployment" Option](#update-the-application-from-v2-to-v3-using-edit-deployment-option)
    - [Edit Deployment](#edit-deployment)
    - [Verify Rollout Status](#verify-rollout-status)
    - [Verify Replicasets](#verify-replicasets)
    - [Verify Rollout History](#verify-rollout-history)
    - [Access the Application using Public IP](#access-the-application-using-public-ip-1)
- [Rollback Deployment](#rollback-deployment)
  - [Rollback a Deployment to previous version](#rollback-a-deployment-to-previous-version)
    - [Check the Rollout History of a Deployment](#check-the-rollout-history-of-a-deployment)
    - [Verify changes in each revision](#verify-changes-in-each-revision)
    - [Rollback to previous version](#rollback-to-previous-version)
    - [Verify Deployment, Pods, ReplicaSets](#verify-deployment-pods-replicasets)
    - [Access the Application using Public IP](#access-the-application-using-public-ip-2)
  - [Rollback to specific revision](#rollback-to-specific-revision)
    - [Check the Rollout History of a Deployment](#check-the-rollout-history-of-a-deployment-1)
    - [Rollback to specific revision](#rollback-to-specific-revision-1)
    - [List Deployment History](#list-deployment-history)
    - [Access the Application using Public IP](#access-the-application-using-public-ip-3)
  - [Step-03: Rolling Restarts of Application](#step-03-rolling-restarts-of-application)
- [Pause \& Resume Deployments](#pause--resume-deployments)
  - [Pausing \& Resuming Deployments](#pausing--resuming-deployments)
    - [Check current State of Deployment \& Application](#check-current-state-of-deployment--application)
    - [Pause Deployment and Two Changes](#pause-deployment-and-two-changes)
    - [Resume Deployment](#resume-deployment)
    - [Access Application](#access-application)
- [Clean-Up](#clean-up)


# Kubernetes - Deployment

## Create Deployment
- Create Deployment to rollout a ReplicaSet

```
# Create Deployment

kubectl create deployment my-first-deployment --image=nginx 

# Verify Deployment
kubectl get deployments
kubectl get deploy 

# Describe Deployment
kubectl describe deployment my-first-deployment

# Verify ReplicaSet
kubectl get rs

# Verify Pod
kubectl get po
```
## Scaling a Deployment
- Scale the deployment to increase the number of replicas (pods)
```
# Scale Up the Deployment
kubectl scale --replicas=20 deployment/<Deployment-Name>
kubectl scale --replicas=20 deployment/my-first-deployment 

# Verify Deployment
kubectl get deploy

# Verify ReplicaSet
kubectl get rs

# Verify Pods
kubectl get po

# Scale Down the Deployment
kubectl scale --replicas=10 deployment/my-first-deployment 
kubectl get deploy
```

## Expose Deployment as a Service

```
# Expose Deployment as a Service
kubectl expose deployment my-first-deployment --type=NodePort --port=80 --target-port=80 --name=my-first-deployment-service

# Get Service Info
kubectl get svc
Observation: Make a note of port which starts with 3 (Example: 80:3xxxx/TCP). Capture the port 3xxxx and use it in application URL below. 

# Get Public IP of Worker Nodes
kubectl get nodes -o wide
Observation: Make a note of "EXTERNAL-IP" if your Kubernetes cluster.
```
- **Access the Application using Public IP**
```
http://<worker-node-public-ip>:<Node-Port>
```

# Kubernetes - Update Deployments

## Updating Application version V1 to V2 using "Set Image" Option
### Update Deployment
- **Observation:** Please Check the container name in `spec.container.name` yaml output and make a note of it and 
replace in `kubectl set image` command <Container-Name>
```
# Get Container Name from current deployment
kubectl get deployment my-first-deployment -o yaml

# Update Deployment
kubectl set image deployment/my-first-deployment nginx --record=true
```
### Verify Rollout Status (Deployment Status)
- **Observation:** By default, rollout happens in a rolling update model, so no downtime.
```
# Verify Rollout Status 
kubectl rollout status deployment/my-first-deployment

# Verify Deployment
kubectl get deploy
```
### Describe Deployment
- **Observation:**
  - Verify the Events and understand that Kubernetes by default do  "Rolling Update"  for new application releases. 
  - With that said, we will not have downtime for our application.
```
# Descibe Deployment
kubectl describe deployment my-first-deployment
```
### Verify ReplicaSet
- **Observation:** New ReplicaSet will be created for new version
```
# Verify ReplicaSet
kubectl get rs
```

### Verify Pods
- **Observation:** Pod template hash label of new replicaset should be present for PODs letting us 
know these pods belong to new ReplicaSet.
```
# List Pods
kubectl get po
```

### Verify Rollout History of a Deployment
- **Observation:** We have the rollout history, so we can switch back to older revisions using 
revision history available to us.  

```
# Check the Rollout History of a Deployment
kubectl rollout history deployment/<Deployment-Name>
kubectl rollout history deployment/my-first-deployment  
```

### Access the Application using Public IP
```
# Get NodePort
kubectl get svc

# Get Public IP of Worker Nodes
kubectl get nodes -o wide

# Application URL
http://<worker-node-public-ip>:<Node-Port>
```


## Update the Application from V2 to V3 using "Edit Deployment" Option
### Edit Deployment
```
# Edit Deployment
kubectl edit deployment/my-first-deployment --record=true
```


### Verify Rollout Status
```
# Verify Rollout Status 
kubectl rollout status deployment/my-first-deployment
```
### Verify Replicasets
```
# Verify ReplicaSet and Pods
kubectl get rs
kubectl get po
```
### Verify Rollout History
```
# Check the Rollout History of a Deployment
kubectl rollout history deployment/<Deployment-Name>
kubectl rollout history deployment/my-first-deployment   
```

### Access the Application using Public IP
```
# Get NodePort
kubectl get svc


# Get Public IP of Worker Nodes
kubectl get nodes -o wide
Observation: Make a note of "EXTERNAL-IP" if your Kubernetes cluster.

# Application URL
http://<worker-node-public-ip>:<Node-Port>
```
# Rollback Deployment

## Rollback a Deployment to previous version

### Check the Rollout History of a Deployment
```
# List Deployment Rollout History

kubectl rollout history deployment/my-first-deployment  
```

### Verify changes in each revision

```
# List Deployment History with revision information
kubectl rollout history deployment/my-first-deployment --revision=1
kubectl rollout history deployment/my-first-deployment --revision=2
kubectl rollout history deployment/my-first-deployment --revision=3
```


### Rollback to previous version

```
# Undo Deployment
kubectl rollout undo deployment/my-first-deployment

# List Deployment Rollout History
kubectl rollout history deployment/my-first-deployment  
```

### Verify Deployment, Pods, ReplicaSets
```
kubectl get deploy
kubectl get rs
kubectl get po
kubectl describe deploy my-first-deployment
```

### Access the Application using Public IP
```
# Get NodePort
kubectl get svc


# Get Public IP of Worker Nodes
kubectl get nodes -o wide

# Application URL
http://<worker-node-public-ip>:<Node-Port>
```


## Rollback to specific revision
### Check the Rollout History of a Deployment
```
# List Deployment Rollout History
kubectl rollout history deployment/my-first-deployment 
```
### Rollback to specific revision
```
# Rollback Deployment to Specific Revision
kubectl rollout undo deployment/my-first-deployment --to-revision=3
```

### List Deployment History
```
# List Deployment Rollout History
kubectl rollout history deployment/my-first-deployment  
```


### Access the Application using Public IP
```
# Get NodePort
kubectl get svc

# Get Public IP of Worker Nodes
kubectl get nodes -o wide

# Application URL
http://<worker-node-public-ip>:<Node-Port>
```

## Step-03: Rolling Restarts of Application
- Rolling restarts will kill the existing pods and recreate new pods in a rolling fashion. 
```
# Rolling Restarts
kubectl rollout restart deployment/my-first-deployment

# Get list of Pods
kubectl get po
```


# Pause & Resume Deployments

## Pausing & Resuming Deployments
### Check current State of Deployment & Application
 ```
# Check the Rollout History of a Deployment
kubectl rollout history deployment/my-first-deployment  

# Get list of ReplicaSets
kubectl get rs

# Access the Application 
http://<worker-node-ip>:<Node-Port>
```

### Pause Deployment and Two Changes
```
# Pause the Deployment
kubectl rollout pause deployment/my-first-deployment

# Update Deployment - Application Version from V3 to V4
kubectl set image deployment/my-first-deployment nginx:4.0.0 --record=true

# Check the Rollout History of a Deployment
kubectl rollout history deployment/my-first-deployment  


# Get list of ReplicaSets
kubectl get rs

# Make one more change: set limits to our container
kubectl set resources deployment/my-first-deployment -c=kubenginx --limits=cpu=20m,memory=30Mi
```
### Resume Deployment 
```
# Resume the Deployment
kubectl rollout resume deployment/my-first-deployment

# Check the Rollout History of a Deployment
kubectl rollout history deployment/my-first-deployment  

# Get list of ReplicaSets
kubectl get rs
```
### Access Application
```
# Access the Application 
http://<node1-public-ip>:<Node-Port>
```


# Clean-Up
```
# Delete Deployment
kubectl delete deployment my-first-deployment

# Delete Service
kubectl delete svc my-first-deployment-service

# Get all Objects from Kubernetes default namespace
kubectl get all
```





