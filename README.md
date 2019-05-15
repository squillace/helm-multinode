# Helm and Helm 3 with multi-OS Kubernetes clusters

This repository has a number of walkthroughs intended to help you get up to speed with Helm 2.X, the upcoming alpha of Helm 3.0, as well as the issues that arise with both Helm charts and manifests when used with Kubernetes clusters that have both Windows nodes and Linux nodes, a configuration supported in Kubernetes 1.14. The organization of the repo is as follows.

`NOTE`: This repository assumes you're working from a bash/zsh type console, with the standard Linux tools. PowerShell works fine for the same scripting work, and PRs to that effect are welcome.

1. The first directory, **`Setup on AKS`** describes setting up the infrastructure for the demo. Nothing in this entire workshop is specific to Azure, with the sole exception of the creation mechanism for the cluster and the acquisition of credentials. Any Kubernetes cluster will work for this directory.
2. The second directory, **`HelloHelm2`**, describes basic operations with the current Helm 2.13.1, including the proper installation of Tiller for a developer with their own isolated cluster. 
3. The third directory, **`HelloIIS`**, begins by adding a Windows NodePool to the AKS cluster, and then introduces both manifests and Helm charts used with Windows containers, explains the differences between Windows and Linux applications, and shows how the cluster must be operated to ensure a good working experience with both types of applications. 
3. The fourth directory, **`HelloHelm3`**, describes the proper installation of a canary build of Helm 3 so that it does not collide with the current version of Helm 2, and illustrates the basic features of Helm 3 with the same applications used in **`HelloHelm2`** and **`HelloIIS`**. 



1. set up AKS linux cluster
2. Set up windows nodepool
3. ensure RBAC
4. apply the taints
5. install Helm 2 in default role but on windows cluster
6. do the helm 2 dance: searching, installation, from communal and git cloned repos. upgrade to scale. delete. install neo4j, mongodb, and so on.
7. do the helm2 dance against windows workloads. discuss the taint; discuss the tolerations and what failures look like. Helm2 search, install, and so on.
8. move to helm3 dance. install on linux or mac (https://storage.googleapis.com/kubernetes-helm/helm-dev-v3-<darwin|linux|windows>-amd64.tar.gz)