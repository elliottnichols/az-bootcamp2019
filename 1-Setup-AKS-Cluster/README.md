# AKS Cluster

## (Option 1) Provided AKS Cluster
Due to the permission limits and time required to register features, AKS clusters have been made available for groups of 2-3 to complete this lab. Coordinate with your group members to apply cluster-wide changes. Individual users can connect and play around with cluster deployments.

URLs for Available Clusters

https://azbootcampcincy.blob.core.windows.net/bootcamp/config_group0/config_group0

```shell
$ export CLUSTER_NAME=<NAME_YOUR_CLUSTER>

# Get AKS credentials
$ curl -o ~/.kube/config_$CLUSTER_NAME https://azbootcampcincy.blob.core.windows.net/bootcamp/$CLUSTER_NAME/config_$CLUSTER_NAME

# Configure kubectl to use context (cluster)
$ kubectl config use-context $CLUSTER_NAME-admin

# Confirm Connectivity to AKS cluster
$ kubectl get nodes
NAME                            STATUS   ROLES   AGE   VERSION
aks-pool1-70479103-vmss000000   Ready    agent   79m   v1.13.5
aks-pool1-70479103-vmss000001   Ready    agent   79m   v1.13.5

# Checkout Git Repo
$ cd ~/<some_directory>
$ git clone https://github.com/elliottnichols/az-bootcamp2019.git
$ cd az-bootcamp2019/1-Setup-AKS-Cluster

# Add Tiller service account and role binding
$ kubectl create -f role-binding.yaml

# Add permissions for lab
$ kubectl create clusterrolebinding --user system:serviceaccount:kube-system:default kube-system-cluster-admin --clusterrole cluster-admin
# Initialize Helm and add coreos helm repo
$ helm init --service-account tiller
$ helm repo add coreos https://s3-eu-west-1.amazonaws.com/coreos-charts/stable/
$ helm repo update
```

## (Option 2) Personal AKS Cluster
Setup your own AKS cluster in your own Subscription. This requires elevated contributor permissions in the Azure Subscription as well as permissions to create Service Principals on the Azure AD Tenant. 

```bash
$ export CLUSTER_NAME=<NAME_YOUR_CLUSTER>
$ az group create -n $CLUSTER_NAME -l eastus2

# Create AKS Cluster
$ az group create -n $CLUSTER_NAME -l eastus2
$ az aks create \
    --resource-group $CLUSTER_NAME \
    --name $CLUSTER_NAME \
    --location eastus2 \
    --node-count 2 \
    --nodepool-name pool1 \
    --node-vm-size Standard_F2s \
    --admin-username azureuser \
    --kubernetes-version 1.13.5 \
    --ssh-key-value ~/.ssh/id_rsa.pub

# Experimental Flags: Won't work for this lab
    # --enable-vmss \
    # --node-zones 1 2 3 \
    # --enable-cluster-autoscaler \
    # --min-count 2 \
    # --max-count 5 \

# Get AKS credentials from AKS cluster
$ az aks get-credentials -n $CLUSTER_NAME -g $CLUSTER_NAME -f ~/.kube/config_$CLUSTER_NAME --admin

# Set KUBECONFIG env var to the default `config` file and lab cluster
## Note: Adjust as necessary with existing k8s config file(s)/env vars
$ export KUBECONFIG=$KUBECONFIG:$HOME/.kube/config:$HOME/.kube/config_$CLUSTER_NAME
``` 

