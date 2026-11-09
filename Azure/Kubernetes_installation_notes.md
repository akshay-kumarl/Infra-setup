# Kubernetes documentation

## K8s installation in AWS linux machine 
     sudo yum update
     
     kubectl version --client
     
     
     eksctl version



# K8s installation in Ubuntu machine


# AKS Installation on an Ubuntu VM in Azure

This guide explains how to create and manage an Azure Kubernetes Service
(AKS) cluster from an Ubuntu VM using Azure CLI and `kubectl`.

> **Important:** AKS is a managed Kubernetes service. Azure manages the
> Kubernetes control plane. Your Ubuntu VM is the administration
> machine; you do not install the AKS control plane on it.

## 1. Architecture

``` text
Ubuntu VM in Azure
  ├── Azure CLI
  └── kubectl
        |
        v
Azure Kubernetes Service (AKS)
  ├── Managed Kubernetes control plane
  └── Worker node pool (Azure VM Scale Set)
        |
        v
Kubernetes application
  ├── Deployment
  ├── Pods
  └── LoadBalancer Service
```

## 2. Prerequisites

-   An Azure subscription.
-   An Ubuntu 22.04 or 24.04 VM.
-   SSH access to the Ubuntu VM.
-   Azure permissions to create AKS clusters and associated resources.
-   Sufficient regional VM quota and SKU availability.

The example uses **Central India** and one worker node for learning.
Check current pricing and quota before creating resources.

## 3. Update Ubuntu

Connect to your Ubuntu VM using SSH.

``` bash
sudo apt update
sudo apt upgrade -y
sudo apt install -y curl apt-transport-https ca-certificates gnupg lsb-release
lsb_release -a
```

## 4. Install Azure CLI

``` bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
az version
```

## 5. Sign in to Azure

For a VM with browser access:

``` bash
az login
```

For a headless VM, use device-code authentication:

``` bash
az login --use-device-code
```

Open the URL displayed in the terminal on your computer, enter the code,
and complete sign-in.

View your account and subscriptions:

``` bash
az account show
az account list -o table
```

Select your subscription:

``` bash
az account set --subscription "YOUR_SUBSCRIPTION_ID"
```

Confirm the selected subscription:

``` bash
az account show --query "{name:name, id:id, tenantId:tenantId}" -o table
```

## 6. Create a resource group

``` bash
az group create \
  --name rg-aks-demo \
  --location centralindia
```

Verify:

``` bash
az group list -o table
```

## 7. Register Azure resource providers

``` bash
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Compute
az provider register --namespace Microsoft.Network
az provider register --namespace Microsoft.ManagedIdentity
```

Check the AKS provider registration state:

``` bash
az provider show \
  --namespace Microsoft.ContainerService \
  --query registrationState \
  -o tsv
```

Wait until the result is `Registered`.

## 8. Check VM SKU availability and quota

Check whether the example VM size is available in Central India:

``` bash
az vm list-skus \
  --location centralindia \
  --resource-type virtualMachines \
  --size Standard_D2s_v5 \
  --all \
  -o table
```

Also check regional vCPU quota in the Azure Portal under **Subscriptions
→ Usage + quotas**. The SKU must be available and your subscription must
have sufficient quota.

If `Standard_D2s_v5` is unavailable or quota is insufficient, select an
eligible VM size and use it in the cluster creation command.

## 9. Create the AKS cluster

Example configuration:

  Setting          Value
  ---------------- -------------------
  Resource group   `rg-aks-demo`
  AKS cluster      `aks-demo`
  Region           `centralindia`
  Node count       `1`
  Node VM size     `Standard_D2s_v5`
  Node OS          Ubuntu
  Identity         Managed identity

Create the cluster:{without publicip}

``` bash
az aks create \
  --resource-group rg-aks-demo \
  --name aks-demo \
  --location centralindia \
  --node-count 1 \
  --node-vm-size Standard_D2s_v5 \
  --os-sku Ubuntu \
  --enable-managed-identity \
  --generate-ssh-keys
```

Cluster creation can take several minutes.

Check the cluster:

``` bash
az aks show \
  --resource-group rg-aks-demo \
  --name aks-demo \
  --output table
```

```
to create a cluster with publicip with present resource group
az aks create \
  --resource-group rg-aks-demo \
  --name aks-demo \
  --location centralindia \
  --node-count 2 \
  --node-vm-size Standard_D2s_v5 \
  --os-sku Ubuntu \
  --zones 1 2 \
  --enable-managed-identity \
  --enable-node-public-ip \
  --allowed-host-ports 30000 \
  --generate-ssh-keys



to add node to present cluster 
az aks nodepool add \
  --resource-group myresourcegrp \
  --cluster-name aks-demo \
  --name pubpool \
  --node-count 2 \
  --node-vm-size Standard_D2ls_v6 \
  --os-sku Ubuntu \
  --zones 1 2 \
  --enable-node-public-ip \
  --allowed-host-ports 31000/TCP
```


> **Cost note:** The AKS Free tier does not make worker VMs, disks,
> networking, load balancers, or other Azure resources free. These can
> incur charges. A one-node cluster is for basic learning, not
> production high availability.

## 10. Install kubectl

``` bash
az aks install-cli
```

If you receive a permissions error because the install location requires
elevated privileges:

``` bash
sudo az aks install-cli
```

Verify:

``` bash
kubectl version --client
```

## 11. Connect Ubuntu to the AKS cluster

Download the cluster credentials and merge them into your kubeconfig:

``` bash
az aks get-credentials \
  --resource-group rg-aks-demo \
  --name aks-demo
```

Check the current context:

``` bash
kubectl config current-context
```

List worker nodes:

``` bash
kubectl get nodes -o wide
```

The node should eventually show `Ready`.

If you need to replace an existing context with the downloaded
credentials, add `--overwrite-existing` to the `az aks get-credentials`
command.

## 12. Verify Kubernetes components

List namespaces:

``` bash
kubectl get namespaces
```

List Pods in all namespaces:

``` bash
kubectl get pods -A
```

Check cluster information:

``` bash
kubectl cluster-info
```

Check nodes:

``` bash
kubectl get nodes
```

## 13. Deploy an NGINX application

Create an NGINX Deployment:

``` bash
kubectl create deployment nginx --image=nginx
```

Check the Deployment and Pods:

``` bash
kubectl get deployments
kubectl get pods
```

Wait for the Pod to become ready:

``` bash
kubectl rollout status deployment/nginx
```

View Pod details:

``` bash
kubectl describe pods -l app=nginx
```

## 14. Expose NGINX using an Azure LoadBalancer

Create a Kubernetes Service of type `LoadBalancer`:

``` bash
kubectl expose deployment nginx \
  --port=80 \
  --target-port=80 \
  --type=LoadBalancer
```

Check the Service:

``` bash
kubectl get services
```

Watch until Azure assigns an external IP:

``` bash
kubectl get service nginx --watch
```

Once an external IP appears, open this address in your browser:

``` text
http://YOUR_EXTERNAL_IP
```

You should see the NGINX welcome page.

> **Cost note:** A LoadBalancer Service provisions Azure networking
> resources and may incur additional charges. Delete the Service when
> you no longer need it.

## 15. Useful AKS and kubectl commands

  ---------------------------------------------------------------------------------------
  Task                                Command
  ----------------------------------- ---------------------------------------------------
  List AKS clusters                   `az aks list -o table`

  Show AKS cluster                    `az aks show -g rg-aks-demo -n aks-demo -o table`

  Get nodes                           `kubectl get nodes -o wide`

  Get Pods in all namespaces          `kubectl get pods -A`

  Get Deployments                     `kubectl get deployments`

  Get Services                        `kubectl get services`

  Describe a Pod                      `kubectl describe pod POD_NAME`

  View Pod logs                       `kubectl logs POD_NAME`

  Scale NGINX to three replicas       `kubectl scale deployment nginx --replicas=3`

  Check rollout                       `kubectl rollout status deployment/nginx`

  Delete NGINX Deployment             `kubectl delete deployment nginx`
  ---------------------------------------------------------------------------------------

Replace `POD_NAME` with the actual Pod name returned by
`kubectl get pods`.

## 16. Clean up resources

Delete the LoadBalancer Service:

``` bash
kubectl delete service nginx
```

Delete the Deployment:

``` bash
kubectl delete deployment nginx
```

When you have finished the exercise, delete the resource group:

``` bash
az group delete \
  --name rg-aks-demo \
  --yes \
  --no-wait
```

This deletes the AKS cluster and resources contained in that resource
group. Check for separately created resources before assuming all
related costs have stopped.

## 17. What you learned

After completing this practical, you will have practiced:

-   Installing Azure CLI on Ubuntu.
-   Authenticating to an Azure subscription.
-   Creating a resource group.
-   Registering Azure resource providers.
-   Checking VM SKU availability and quota.
-   Creating an AKS cluster with managed identity.
-   Installing and configuring `kubectl`.
-   Connecting to AKS from Ubuntu.
-   Inspecting Kubernetes nodes, namespaces, and Pods.
-   Deploying NGINX.
-   Exposing an application using an Azure LoadBalancer.
-   Cleaning up Azure resources.

## 18. AKS vs. Kubernetes with kubeadm

  -----------------------------------------------------------------------
  AKS                                 Kubernetes with kubeadm
  ----------------------------------- -----------------------------------
  Azure manages the control plane.    You manage the control plane.

  Azure provisions and manages AKS    You provision and manage the VMs.
  worker infrastructure.              

  Connect using Azure CLI and         Install and configure Kubernetes
  `kubectl`.                          components yourself.

  Provides Azure identity and         Networking and identity need to be
  networking integrations.            configured and managed separately.
  -----------------------------------------------------------------------

**Remember:** You are not installing Kubernetes control-plane components
directly on your Ubuntu VM. You are using that VM to administer an AKS
cluster managed by Azure.

## 19. Official Microsoft documentation

-   [Create an AKS
    cluster](https://learn.microsoft.com/azure/aks/learn/quick-kubernetes-deploy-cli)
-   [Install Azure CLI on
    Linux](https://learn.microsoft.com/cli/azure/install-azure-cli-linux)
-   [AKS CLI reference](https://learn.microsoft.com/cli/azure/aks)
