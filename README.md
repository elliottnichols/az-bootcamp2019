# az-bootcamp2019
Azure Bootcamp 2019 - AKS and beyond

```
$ export CLUSTER_NAME=az-bootcamp2019
$ az login
$ az account list
$ az account set -s <sub_id>

# add or update AKS CLI extension
$ az extension add --name aks-preview
$ az extension update --name aks-preview



$ az feature register --name VMSSPreview --namespace Microsoft.ContainerService
$ az feature register --name AvailabilityZonePreview --namespace Microsoft.ContainerService
$ az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService
$ az feature list --namespace Microsoft.ContainerService | grep 'VMSSPreview\|AvailabilityZonePreview\|MultiAgentpoolPreview'
$ az provider register -n Microsoft.ContainerService

$ az group create -n $CLUSTER_NAME -l eastus2
$ az aks create \
    --resource-group $CLUSTER_NAME \
    --name $CLUSTER_NAME \
    --location eastus2 \
    --node-count 2 \
    --node-zones 1 2 3 \
    --enable-cluster-autoscaler \
    --min-count 2 \
    --max-count 5 \
    --nodepool-name pool1 \
    --enable-vmss \
    --node-vm-size Standard_F2s \
    --enable-addons monitoring \
    --admin-username azureuser \
    --kubernetes-version 1.13.5 \
    --ssh-key-value ~/.ssh/id_rsa.pub


    --network-plugin kubenet \
    --network-policy calico \


az aks create     --resource-group dj0409mp     --name dj0409mp-cluster     --enable-vmss     --node-count 3     --node-vm-size Standard_D8s_v3     --enable-addons monitoring     --admin-username azureuser     --ssh-key-value az.pub     --node-osdisk-size 100     --kubernetes-version 1.12.7