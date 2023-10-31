- [Kube Config](#kube-config)
  - [Information required in kubeconfig to connect to the cluster](#information-required-in-kubeconfig-to-connect-to-the-cluster)
  - [Modes to connect kubernetes cluster with kubeconfig file](#modes-to-connect-kubernetes-cluster-with-kubeconfig-file)
    - [Kubectl Context](#kubectl-context)
    - [Environment Variable](#environment-variable)
    - [Command-Line Reference](#command-line-reference)
  - [Merging multiple kubeconfig files](#merging-multiple-kubeconfig-files)



# Kube Config
- A file that is used to configure access to a cluster. The default location of the Kubeconfig file is $HOME/.kube/config
-  kubernetes cluster components like controller manager, scheduler and kubelet use the kubeconfig files to interact with the API server



## Information required in kubeconfig to connect to the cluster

- certificate-authority-data: Cluster CA
- server: Cluster endpoint (IP/DNS of the master node)
- name: Cluster name
- user: name of the user/service account.
- token: Secret token of the user/service account.

## Modes to connect kubernetes cluster with kubeconfig file

- Kubectl Context
- Environment Variable
- Command-Line Reference

### Kubectl Context

```
mv /path/to/kubeconfig ~/.kube
```

The default location of the Kubeconfig file is $HOME/.kube/config

```
kubectl version --client

kubectl config get-contexts

kubectl config use-context <cluster-name>  
kubectl config use-context qa-cluster

kubectl get nodes

```


### Environment Variable


```
export KUBECONFIG=$HOME/.kube/dev_cluster_config

export KUBECONFIG=$HOME/.kube/prod_config.yaml
export KUBECONFIG=$HOME/.kube/qa_config.yaml


kubectl config get-contexts

kubectl get nodes



```

### Command-Line Reference

```
kubectl get nodes --kubeconfig=$HOME/.kube/dev_cluster_config
kubectl get nodes --kubeconfig=$HOME/.kube/prod_config.yaml
kubectl get nodes --kubeconfig=$HOME/.kube/qa_config.yaml



KUBECONFIG=$HOME/.kube/dev_cluster_config kubectl get nodes
KUBECONFIG=$HOME/.kube/prod_config.yaml kubectl get nodes
KUBECONFIG=$HOME/.kube/qa_config.yaml kubectl get nodes

kubectl config view --minify



```


## Merging multiple kubeconfig files

Let’s assume you have three Kubeconfig files in the $HOME/.kube/ directory.

- config (default kubeconfig)
- dev_config
- qa_config


```
KUBECONFIG=config:dev_config:qa_config kubectl config view --merge --flatten > config.new
KUBECONFIG=$HOME/.kube/config:$HOME/.kube/prod_config.yaml:$HOME/.kube/qa_config.yaml kubectl config view --merge --flatten=true > $HOME/.kube/config.new

mv $HOME/.kube/config $HOME/.kube/config.old

mv $HOME/.kube/config.new $HOME/.kube/config

kubectl config get-contexts -o name

kubectl config get-contexts

kubectl config use-context <context name>

kubectl config view --minify


kubectl get nodes
```




