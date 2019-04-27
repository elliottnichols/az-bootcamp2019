# Prereqs for Lab
Let's prepare for this lab by installing and configuring all the necessary tools for connectivity

## Install Local Dependencies
 - Azure CLI
 - Kubernetes Command Line Tool, kubectl
 - Helm

### Azure CLI
Azure Command Line Interface to manage Azure resources.

 - az Mac - via [brew](https://brew.sh)
```shell
$ brew update && brew install azure-cli
```

 - [az Windows .MSI Installer](https://aka.ms/installazurecliwindows)

 - [Others](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)

### Kubernetes Command Line Tool, kubectl 
The Kubernetes CLI, `kubectl` (Kube-Control) will managed and operate Kubernetes Cluster(s) from your local machine.

 - kubectl Mac - via [brew](https://brew.sh)
```shell
$ brew install kubernetes-cli
```
- kubectl Windows - via [chocolatey](Chocolatey)
```
- # choco install kubernetes-cli
```
- [kubectl Windows Native](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-windows)
- [kubectl Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-linux)
- [kubectl mac native, macports, powershell gallery, others](https://kubernetes.io/docs/tasks/tools/install-kubectl)

### Helm
Package manager for templated & versioned k8s deployments

 - Helm Mac - via [brew](https://brew.sh)
```shell
$ brew install kubernetes-helm
```
 - Helm Windows
 ```
 # choco install kubernetes-helm
```
 - [Others](https://github.com/helm/helm#install)

### Istio CTL
 - istioctl Mac or Linux
```shell
$ curl -L https://git.io/getLatestIstio | ISTIO_VERSION=1.1.4 sh -

# copy istioctl (in bin directory) binary to folder included in PATH
$ cp bin/istioctl ~/bin/    # for example

```
- [Others](https://github.com/istio/istio/releases)

## Confirm Pre-reqs are installed
```shell
$ az --version
$ kubectl version
$ helm version -c
$ istioctl version
```

## Azure CLI Login
```shell
$ az login
$ az account list
$ az account set -s <YOUR_SUBSCRIPTION_ID>
```
