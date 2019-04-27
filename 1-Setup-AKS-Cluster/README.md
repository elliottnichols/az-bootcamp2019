# AKS Cluster

## (Option 1) Provided AKS Cluster
Due to the permission limits and time required to register features, AKS clusters have been made available for groups of 2-3 to complete this lab. Coordinate with your group members to apply cluster-wide changes. Individual users can connect and play around with cluster deployments.

URLs for Available Clusters


```shell
$ CLUSTER_NAME=<NAME_YOUR_CLUSTER>

# Get AKS credentials from download
$ cp ~/Downloads/config_$CLUSTER_NAME ~/.kube/config_$CLUSTER_NAME -a

# Confirm Connectivity to AKS cluster
$ kubectl get nodes
NAME                            STATUS   ROLES   AGE   VERSION
aks-pool1-70479103-vmss000000   Ready    agent   79m   v1.13.5
aks-pool1-70479103-vmss000001   Ready    agent   79m   v1.13.5

# Add Tiller service account and role binding
$ kubectl create -f 1-Setup-AKS-Cluster/role-binding.yaml

# Wonky permissions
$ kubectl create clusterrolebinding --user system:serviceaccount:kube-system:default kube-system-cluster-admin --clusterrole cluster-admin
# Initialize Helm and add coreos helm repo
$ helm init --service-account tiller$ helm repo add coreos https://s3-eu-west-1.amazonaws.com/coreos-charts/stable/

```

## (Option 2) Personal AKS Cluster
Setup your own AKS cluster in your own Subscription. This requires elevated contributor permissions in the Azure Subscription as well as permissions to create Service Principals on the Azure AD Tenant. 

Registering the required Azure features for this lab may take ~30 minutes or more.

```bash
$ CLUSTER_NAME=<NAME_YOUR_CLUSTER>
$ az group create -n $CLUSTER_NAME -l eastus2

# add or update AKS CLI extension
$ az extension add --name aks-preview
$ az extension update --name aks-preview  # Update if it's already installed

# Azure feature registration can take a while
$ az feature register --name VMSSPreview --namespace Microsoft.ContainerService
$ az feature register --name AvailabilityZonePreview --namespace Microsoft.ContainerService
$ az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService

$ # Watch and Wait
$ az feature list --namespace Microsoft.ContainerService | grep 'VMSSPreview\|AvailabilityZonePreview\|MultiAgentpoolPreview'   # Watch until all features are registered

# Continue once registrations are complete
# Will Propogate new features after all new features are registerd
$ az provider register -n Microsoft.ContainerService

# Get AKS credentials from AKS cluster
$ az aks get-credentials -n $CLUSTER_NAME -g $CLUSTER_NAME -f ~/.kube/config_$CLUSTER_NAME --admin

# Set KUBECONFIG env var to the default `config` file and lab cluster
## Note: Adjust as necessary with existing k8s config file(s)/env vars
$ KUBECONFIG=$KUBECONFIG:$HOME/.kube/config:$HOME/.kube/config_$CLUSTER_NAME
``` 

