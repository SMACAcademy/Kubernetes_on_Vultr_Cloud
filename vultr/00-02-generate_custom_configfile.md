- [Generate Kubeconfig File](#generate-kubeconfig-file)
  - [Create a Service Account](#create-a-service-account)
  - [Create a Secret Object for the Service Account](#create-a-secret-object-for-the-service-account)
  - [Create a ClusterRole](#create-a-clusterrole)
  - [Create ClusterRoleBinding](#create-clusterrolebinding)
  - [Get all Cluster Details \& Secrets](#get-all-cluster-details--secrets)
  - [Generate the Kubeconfig With the variables](#generate-the-kubeconfig-with-the-variables)
  - [Validate the generated Kubeconfig](#validate-the-generated-kubeconfig)


# Generate Kubeconfig File

- Create a Service Account
- Create a Secret Object for the Service Account
- Create a ClusterRole
- Create ClusterRoleBinding
- Get all Cluster Details & Secrets
- Generate the Kubeconfig With the variables
- Validate the generated Kubeconfig



## Create a Service Account

```
kubectl -n kube-system create serviceaccount devops-cluster-admin


```

##  Create a Secret Object for the Service Account

```
cat << EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: devops-cluster-admin-secret
  namespace: kube-system
  annotations:
    kubernetes.io/service-account.name: devops-cluster-admin
type: kubernetes.io/service-account-token
EOF

```
##  Create a ClusterRole

```
cat << EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: devops-cluster-admin
rules:
- apiGroups: [""]
  resources:
  - nodes
  - nodes/proxy
  - services
  - endpoints
  - pods
  verbs: ["get", "list", "watch"]
- apiGroups:
  - extensions
  resources:
  - ingresses
  verbs: ["get", "list", "watch"]
EOF

```
##  Create ClusterRoleBinding

```
cat << EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: devops-cluster-admin
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: devops-cluster-admin
subjects:
- kind: ServiceAccount
  name: devops-cluster-admin
  namespace: kube-system
EOF
```

##  Get all Cluster Details & Secrets

```
export SA_SECRET_TOKEN=$(kubectl -n kube-system get secret/devops-cluster-admin-secret -o=go-template='{{.data.token}}' | base64 --decode)

export CLUSTER_NAME=$(kubectl config current-context)

export CURRENT_CLUSTER=$(kubectl config view --raw -o=go-template='{{range .contexts}}{{if eq .name "'''${CLUSTER_NAME}'''"}}{{ index .context "cluster" }}{{end}}{{end}}')

export CLUSTER_CA_CERT=$(kubectl config view --raw -o=go-template='{{range .clusters}}{{if eq .name "'''${CURRENT_CLUSTER}'''"}}"{{with index .cluster "certificate-authority-data" }}{{.}}{{end}}"{{ end }}{{ end }}')

export CLUSTER_ENDPOINT=$(kubectl config view --raw -o=go-template='{{range .clusters}}{{if eq .name "'''${CURRENT_CLUSTER}'''"}}{{ .cluster.server }}{{end}}{{ end }}')

```
##  Generate the Kubeconfig With the variables

```
cat << EOF > devops-cluster-admin-config
apiVersion: v1
kind: Config
current-context: ${CLUSTER_NAME}
contexts:
- name: ${CLUSTER_NAME}
  context:
    cluster: ${CLUSTER_NAME}
    user: devops-cluster-admin
clusters:
- name: ${CLUSTER_NAME}
  cluster:
    certificate-authority-data: ${CLUSTER_CA_CERT}
    server: ${CLUSTER_ENDPOINT}
users:
- name: devops-cluster-admin
  user:
    token: ${SA_SECRET_TOKEN}
EOF

```


##  Validate the generated Kubeconfig

```
kubectl get nodes --kubeconfig=devops-cluster-admin-config 

```