# Setting up a cluster on AKS that can support multiple nodepools

## Enter the NodePools preview using the Azure CLI
You're going to follow the steps located in this document: https://docs.microsoft.com/en-us/azure/aks/use-multiple-node-pools. Specifically:

1. https://docs.microsoft.com/en-us/azure/aks/use-multiple-node-pools#before-you-begin
2. https://docs.microsoft.com/en-us/azure/aks/use-multiple-node-pools#limitations

This may take a moment, so take your time when registering.

## Create your AKS cluster
Then you're going to create your cluster and enable additional nodepools when doing so. 
> **`NOTE:`** Even though the cluster you are creating is a Linux-node cluster, you're going to be creating the cluster to support multiple nodepools on VMSS (`--enable-vmss`) so that you can add a Windows nodepool in [HelloIIS](../HelloIIS/README.md).

First, specify the values you want to use. It's recommended that you 

    az aks create \
        -g $RESOURCE_GROUP \
        -n $CLUSTER_NAME  \
        --windows-admin-password $PASSWORD_WIN \
        --windows-admin-username $USERNAME \
        --location $LOCATION \
        --generate-ssh-keys \
        -c 2 \
        --enable-vmss \
        --nodepool-name linux \
        --network-plugin azure \
        --kubernetes-version 1.13.5 
        --enable-addons monitoring



## Next Steps
Once you have your cluster, type 

> az aks install-cli 

which will install `kubectl` if you do not already have it on your machine.

Then acquire the cluster configuration file by typing:

> az aks get-credentials -g $RESOURCE_GROUP -n $CLUSTER_NAME

and if you type `kubectl config current-context` you should see your cluster appear. If so, you are ready to [practice with Helm 2](../HelloHelm2/README.md).