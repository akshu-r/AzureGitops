# Azure GitOps with Azure DevOps, ArgoCD & Automation Scripts

This project showcases a **multi-microservice application** deployed on Azure Kubernetes Service (AKS) using a **GitOps workflow**.

It demonstrates how to build, containerize, and deploy multiple microservices with Azure DevOps pipelines, Azure Container Registry, and ArgoCD for continuous delivery.

## ⚙️ Architecture
**Developer → Azure Repos → Azure Pipelines → ACR → GitOps Repo → ArgoCD → AKS**

## 🚀 Step-by-Step Implementation
**1. Azure DevOps Setup**

- Configured Azure DevOps as the CI/CD tool.

- Created separate pipelines for each microservice:
```bash
azure-pipelines-vote.yml

azure-pipelines-result.yml

azure-pipelines-worker.yml
```

Each pipeline:

- Builds the microservice Docker image.

- Tags it with the build ID or commit SHA.
- Executes a script (scripts/updateK8sManifest.sh) to update Kubernetes manifests with new image tags.
- This step establishes the “pathway commit”, which acts as a bridge between CI and CD.

- Pushes the image to Azure Container Registry (ACR).


**2. Azure Agent Setup**

- Configured a self-hosted Azure DevOps agent VM for running the pipelines.

- Installed necessary dependencies: docker, kubectl, az cli, git, argocd CLI.

- The agent executes build, push, and GitOps update stages automatically.

**3. AKS Cluster & ArgoCD Configuration**

- Created an AKS cluster using the Azure CLI.

- Installed ArgoCD in the AKS cluster:
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
- Configured applications in ArgoCD to track manifests

**4. Kubernetes Manifests**

- Created YAML files for each service defining:

- Deployment (with container image from ACR)

- Service (ClusterIP/LoadBalancer)

- ConfigMaps and Secrets

All manifests are version-controlled under k8s-specifications/.

**5. GitOps Workflow:**

- ArgoCD watches the GitOps repository for manifest updates.

- When a commit occurs, ArgoCD automatically syncs the desired state to the AKS cluster.

- Kubernetes pulls the latest image from ACR and performs a rolling update.

## 🏁 Conclusion

This project highlights a complete DevOps and GitOps implementation using Azure services.
By integrating Azure Pipelines, ACR, AKS, and Argo CD, it ensures a fully automated, scalable, and observable deployment workflow.
