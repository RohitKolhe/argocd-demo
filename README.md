# GitOps Concept

## Technology Stack

- [Argo CD](https://argo-cd.readthedocs.io/en/stable/)
- [Kustomize](https://kustomize.io/)
- [Helm](https://helm.sh/blog/helm-3-released/)
- [Azure CLI](https://docs.microsoft.com/de-de/cli/azure/)

## Functionality

- Offers a foundational understanding of implementing GitOps in a declarative fashion.
- Demonstrates setting up Argo CD declaratively and tailoring it for various environments with [Kustomize](https://kustomize.io/).
- Illustrates the notion of cluster [bootstrapping](https://argo-cd.readthedocs.io/en/stable/) with an example.

## Structure
```
.
├── README.md
├── clusters
│   ├── apps
│   │   ├── Chart.yaml
│   │   ├── templates
│   │   │   └── database-connector-backend.yaml
│   │   └── values-development.yaml
│   └── provisioning
│       ├── argocd
│       │   ├── base
│       │   │   ├── argocd-install.yaml
│       │   │   ├── argocd_o.yaml
│       │   │   ├── kustomization.yaml
│       │   │   └── namespace.yaml
│       │   └── development
│       │       ├── argocd-configmap-patch.yaml
│       │       └── kustomization.yaml
│       └── bootstrap
│           ├── base
│           │   ├── bootstrap.yaml
│           │   └── kustomization.yaml
│           └── development
│               ├── argocd-app-patch.yaml
│               └── kustomization.yaml
└── resources
    └── deployments
        └── backend
            ├── Chart.yaml
            ├── templates
            │   ├── postgres.yaml
            │   └── webapp.yaml
            └── values-development.yaml
```
clusters: 
  The entirety of Kubernetes cluster provisioning, such as installing Argo CD, bootstrapping the cluster, and defining Argo CD applications to run within it, should be organized within this folder. 
    - argocd: This directory is designated for all resources related to the installation and potential patching of Argo CD. The content of argocd-install.yaml can be sourced from GitHub.
    - bootstrap: Resources relevant to cluster bootstrapping should be placed here.
    - apps: This directory holds the definitions for all Argo CD applications intended to operate within the cluster.

resources:
All definitions for resources, such as Helm configurations, ingress settings, secrets, and monitoring configurations, should be organized within this folder.
   
## Runbook: Provision a cluster
    1. Install argocd for a development environment 
    kubectl apply -k clusters/provisioning/argocd/development

    kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'

    kubectl -n argocd get secret argocd-initial-admin-secret \
          -o jsonpath="{.data.password}" | base64 -d; echo

    2. Create access token secret
    kubectl -n argocd create secret generic dev-ops-token --from-literal=username=<username> --from-literal=password=<password>
    
    3. Bootstrap the cluster for a development environment (create an app of apps)
    kubectl apply -k clusters/provisioning/bootstrap/development


    
    

    

    
