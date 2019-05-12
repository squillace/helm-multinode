# Helm Charts and Manifests with Windows and Kubernetes Nodes

This repository contains walkthroughs that illustrate some of the issues with Kubernetes Clusters that have mixed node types and shows methods of getting around them properly.

At the moment, most cluster provisioning tools are creating clusters that by default use exclusively Linux nodes. Version 1.14 of Kubernetes, however, brought Windows nodes into the project officially. 



AKS is rbac'd by default
	rules for helm must be done first
	k apply tiller-rbac.yaml for the helm version you're using
	helm version to test installation
	`kubectl get no -l beta.kubernetes.io/os=windows -o jsonpath='{range .items[*]}{.metadata.name}{"\n"}' | xargs -I {} kubectl taint nodes {} windows=true:NoSchedule` will taint all Windows nodes at once.
	`k taint nodes <windows node name> os=windows:NoSchedule` to each windows node
	every application that is scheduled for windows THEN uses the toleration as applied in either helloiis.withTolerations.yaml in the repo OR as the helm chart does it in the repo. Either will work.



[12:06 PM] Ralph Squillace (CONTAINERS)
    teszting for rbac is kubectl api-versions | grep rbac and you should see 

​[12:07 PM] Ralph Squillace (CONTAINERS)
    rbac.authorization.k8s.io/v1

rbac.authorization.k8s.io/v1beta1

 

