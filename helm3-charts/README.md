# Using Charts with Helm 3

As mentioned before, there are some differences when creating charts with Helm 3 compared to Helm 2. This is a quick tutorial about the basic processes that uses Azure Container Service, which supports Helm 3 registries. However, you can run a Helm 3 chart repository locally as well. That and more is discussed in the more complete [Helm 3 registry introduction](https://github.com/jdolitsky/helm3-registry-intro) by Josh Dolitsky.

This is a shorter version to get you started working with Helm 3 compliant charts.

# Creating a chart with Helm 3

While most chart commands use `helm chart <command>` form, creating a chart is shorter:

    helm3 create nginx-helm3

This creates a starter chart that installs nginx that should look like this:

```
$ tree

.
├── README.md
└── nginx-helm3
    ├── Chart.yaml
    ├── charts
    ├── templates
    │   ├── NOTES.txt
    │   ├── _helpers.tpl
    │   ├── deployment.yaml
    │   ├── ingress.yaml
    │   └── service.yaml
    └── values.yaml

3 directories, 8 files
```

Install the chart:

    helm3 install nginx-helm3 nginx-helm3

You should see something like the following.

    NAME: nginx-helm3
    LAST DEPLOYED: 2019-05-19 03:19:07.703728 -0700 PDT m=+4.605105775
    NAMESPACE: default
    STATUS: deployed

    NOTES:
    1. Get the application URL by running these commands:
    export POD_NAME=$(kubectl get pods -l "app=nginx-helm3,release=nginx-helm3" -o jsonpath="{.items[0].metadata.name}")
    echo "Visit http://127.0.0.1:8080 to use your application"
    kubectl port-forward $POD_NAME 8080:80

Note that the instructions to get the POD_NAME are incorrect; to get the POD_NAME variable you type:

    kubectl get pods -l "app.kubernetes.io/instance=nginx-helm3,app.kubernetes.io/name=nginx-helm3" -o jsonpath="{.items[0].metadata.name}"

with that, you can port forward to the pod, and see the result:

    export POD_NAME=$(kubectl get pods -l "app.kubernetes.io/instance=nginx-helm3,app.kubernetes.io/name=nginx-helm3" -o jsonpath="{.items[0].metadata.name}") && kubectl port-forward $POD_NAME 8081:80 -n default

and you should be able to open up http://localhost:8081 and see the result. 

## Pushing and pulling charts with ACR 

Let's do something with our new chart. First, let's log into an Azure Container Registry (ACR). 

    az acr login -n <registry name>

Once that's done, you can now save your Helm 3 chart in the local cache with two arguments: the path to the chart, which is merely the local chart directory (`nginx-helm3` in this case) and the identity of the chart, which includes the OCI registry qualifier (<registry name>.azurecr.io -- in this example, with the `squillace` registry) and the directory in which the chart resides, in this case `/helm3/nginx-helm3:v1`.

    helm3 chart save nginx-helm3 squillace.azurecr.io/helm3/nginx-helm3:v1

The results should look like this:

    5d30a75: Saving content (2.2 KiB)
    Name: nginx-helm3
    Version: 0.1.0
    Meta: sha256:42603b382336019d658ec8c0c71c57be421dc49a471ce6c6b776d7834e54cbec
    Content: sha256:5d30a757c47d81dd0a58578e1be31d31f4440d338e0f1a0bc20928c27f796ae7
    v1: saved

You'll note that the version of `0.1.0` is taken from the version specified in the nginx-helm3/Chart.yaml file. 

Now you can see it in your local cache with 

    helm3 chart list

which results in (this example uses the `squillace` registry)

    REF                                      	NAME       	VERSION	DIGEST 	SIZE   	CREATED
    squillace.azurecr.io/helm3/nginx-helm3:v1	nginx-helm3	0.1.0  	5d30a75	2.2 KiB	7 minutes

Let's push the chart to ACR -- the same command used for any registry. Replace `squillace` with your registry name.

    helm3 chart push squillace.azurecr.io/helm3/nginx-helm3:v1

Which results in

    The push refers to repository [squillace.azurecr.io/helm3/nginx-helm3]
    Name: nginx-helm3
    Version: 0.1.0
    Meta: sha256:42603b382336019d658ec8c0c71c57be421dc49a471ce6c6b776d7834e54cbec
    Content: sha256:5d30a757c47d81dd0a58578e1be31d31f4440d338e0f1a0bc20928c27f796ae7
    v1: pushed to remote (2 layers, 2.3 KiB total)

## Pulling charts with Helm 3

Now let's destroy the local chart completely.

    helm3 chart remove squillace.azurecr.io/helm3/nginx-helm3:v1

If you type `helm3 chart list` now, the chart is no longer there. Delete the local chart directory with `rm -rf nginx-helm3`, and we have no local chart at all. Let's first pull the chart from ACR:

    helm3 chart pull squillace.azurecr.io/helm3/nginx-helm3:v1

which results in

    Name: nginx-helm3
    Version: 0.1.0
    Meta: sha256:42603b382336019d658ec8c0c71c57be421dc49a471ce6c6b776d7834e54cbec
    Content: sha256:5d30a757c47d81dd0a58578e1be31d31f4440d338e0f1a0bc20928c27f796ae7
    Status: Chart is up to date for squillace.azurecr.io/helm3/nginx-helm3:v1

Now let's write the chart to the local directory so that it can be edited and used:

    helm3 chart export squillace.azurecr.io/helm3/nginx-helm3:v1

which results in 

    Name: nginx-helm3
    Version: 0.1.0
    Meta: sha256:42603b382336019d658ec8c0c71c57be421dc49a471ce6c6b776d7834e54cbec
    Content: sha256:5d30a757c47d81dd0a58578e1be31d31f4440d338e0f1a0bc20928c27f796ae7
    Exported to nginx-helm3/

Now you can again install the chart. First, delete the previous installation with `helm3 delete nginx-helm3`. Then reinstall the local chart with `helm3 install nginx-helm3 nginx-helm3`, and you'll see the chart install into your cluster. 