# Tekton AKS Test Pipelines

## Quickstart

### Prerequisites

* kubectl
* [Kustomize](https://kustomize.io/)
* [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)
* [Azure AD Workload CLI (azwi)](https://azure.github.io/azure-workload-identity/docs/installation/azwi.html)

### Install Azure Workload Identity

``` bash
CLUSTER_RESOURCEGROUP=<your AKS cluster resource group>
CLUSTER_NAME=<your AKS cluster name>
AZURE_ENVIRONMENT=AzurePublicCloud
AZURE_TENANT_ID=<your tenant id>

# Enable OIDC Issuer in AKS
az aks update -g "${CLUSTER_RESOURCEGROUP}" -n "${CLUSTER_NAME}" --enable-oidc-issuer

kustomize build https://github.com/juan-lee/kustomize-library/bases/azure-workload-identity | kubectl apply --server-side -f -
```

### Install Tekton with AKS Test Pipelines

``` bash
kustomize build https://github.com/juan-lee/kustomize-library/tekton | kubectl apply --server-side -f -
```

### Setup Service Account

``` bash
APPLICATION_NAME=<your app name>
CLUSTER_RESOURCEGROUP=<your AKS cluster resource group>
CLUSTER_NAME=<your AKS cluster name>
SERVICE_ACCOUNT_ISSUER=$(az aks show -g "${CLUSTER_RESOURCEGROUP}" -n "${CLUSTER_NAME}" --query "oidcIssuerProfile.issuerUrl" -otsv)

azwi serviceaccount create phase app --aad-application-name "${APPLICATION_NAME}"

azwi serviceaccount create phase sa \
  --aad-application-name "${APPLICATION_NAME}" \
  --service-account-namespace "tekton" \
  --service-account-name "azure-identity"

azwi serviceaccount create phase federated-identity \
  --aad-application-name "${APPLICATION_NAME}" \
  --service-account-namespace "tekton" \
  --service-account-name "azure-identity" \
  --service-account-issuer-url "${SERVICE_ACCOUNT_ISSUER}"
```

### Run a pipeline

``` bash
kubectl create -f https://raw.githubusercontent.com/juan-lee/kustomize-library/main/tekton/examples/k8s-conformance-pipelinerun.yaml 
```

### View the Dashboard

``` bash
kubectl proxy
```

Open the [dashboard](http://localhost:8001/api/v1/namespaces/tekton-pipelines/services/tekton-dashboard:http/proxy/) in your browser.
