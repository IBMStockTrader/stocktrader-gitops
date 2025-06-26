# This folder contains GitHub Actions workflows

Workflows are used to build and deploy the StockTrader application to Azure Kubernetes Service (AKS).

This file describes the workflow that is used to deploy the StockTrader custom resource to AKS using GitOps principles.

Workflow is defined in the [deploy-stocktrader-aks.yml](deploy-stocktrader-aks.yml) file.

## Workflow Configuration
The workflow uses the following environment variables in the `env` section:
```
  # EDIT secrets with your registry, registry path, and credentials
  ACR_NAME: <your-acr-name>
  GITOPS_REPO: <your-gitops-repo>
  GITOPS_DIR: application
  GITOPS_USERNAME: ${{ secrets.GITOPS_USERNAME }}
  GITOPS_TOKEN: ${{ secrets.GITOPS_TOKEN }}
```

GitOps repository is where the StockTrader custom resource file is stored.
The workflow updates that file with the new image locations and tags for all microservices.

## Required Secrets
You need to configure the following secrets in your repository:
```
AZURE_CLIENT_ID - Azure App Registration or Managed Identity client ID
AZURE_TENANT_ID - Azure tenant ID
AZURE_SUBSCRIPTION_ID - Azure subscription ID
GITOPS_TOKEN - GitHub PAT with write access to your GitOps repo
GITOPS_USERNAME - Your GitHub username
ACR_LOGIN_SERVER - Your ACR login server (e.g., <acr-name>.azurecr.io)
```

## Azure Workflow
This workflow:
- Authenticates with Azure using OIDC
- Updates the StockTrader custom resource in the GitOps repository
- Triggers a deployment to AKS using the updated configuration

### Required Azure Setup
1. Create an Azure Container Registry (ACR)
2. Set up AKS cluster with StockTrader operator
3. Configure OIDC authentication between GitHub Actions and Azure
4. Create or fork a GitOps repo for deployment manifests

### Environment Variables
The workflow uses these environment variables:
```
ACR_NAME - Your Azure Container Registry name
GITOPS_REPO - Your GitOps repository (format: username/repo)
GITOPS_DIR - Directory containing deployment manifests
```

## Security Notes
- All sensitive information is stored in Azure Key Vault and injected via external secrets
- The StockTrader custom resource file (`stocktrader-azure-eks-cr.yml`) is designed to be public-safe
- Credentials and secrets are managed through the `cjot-credentials` secret

## Disable other workflows
If this repo contains other workflows you do not need, you can disable or remove them.
To disable a workflow, go to `Actions`, select the workflow, click the `...` menu, and click `Disable workflow`.
You can re-enable the workflow later in the same way. 