- [Kubernetes - Services](#kubernetes---services)
  - [ClusterIP Service - Backend Application Setup](#clusterip-service---backend-application-setup)
  - [NodePort Service - Frontend Application Setup](#nodeport-service---frontend-application-setup)

# Kubernetes - Services

## ClusterIP Service - Backend Application Setup
```
# Create Deployment for Backend Rest App
kubectl create deployment my-backend-rest-app --image=nginx 
kubectl get deploy

# Create ClusterIp Service for Backend Rest App
kubectl expose deployment my-backend-rest-app --port=8080 --target-port=8080 --name=my-backend-service
kubectl get svc
```


## NodePort Service - Frontend Application Setup
- **Nginx Conf File**
```conf
server {
    listen       80;
    server_name  localhost;
    location / {
    # Update your backend application Kubernetes Cluster-IP Service name  and port below      
    # proxy_pass http://<Backend-ClusterIp-Service-Name>:<Port>;      
    proxy_pass http://my-backend-service:8080;
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```
```
# Create Deployment for Frontend Nginx Proxy
kubectl create deployment my-frontend-nginx-app --image=nginx 
kubectl get deploy

# Create ClusterIp Service for Frontend Nginx Proxy
kubectl expose deployment my-frontend-nginx-app  --type=NodePort --port=80 --target-port=80 --name=my-frontend-service
kubectl get svc

# Capture IP and Port to Access Application
kubectl get svc
kubectl get nodes -o wide
http://<node1-public-ip>:<Node-Port>/hello

# Scale backend with 10 replicas
kubectl scale --replicas=10 deployment/my-backend-rest-app

# Test again to view the backend service Load Balancing
http://<node1-public-ip>:<Node-Port>/hello
```

