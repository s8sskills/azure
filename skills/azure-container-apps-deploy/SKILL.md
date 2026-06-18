---
name: azure-container-apps-deploy
description: Use when deploying a serverless container, API, or web service to Azure Container Apps, or when the user mentions "Azure Container Apps", "ACA", "az containerapp", or wants to deploy a serverless container on Azure.
---

# Deploy to Azure Container Apps (ACA)

Azure Container Apps (ACA) is a fully managed serverless container platform built on Kubernetes Event-driven Autoscaling (KEDA). It enables you to run microservices and containerized applications, scaling to zero dynamically without managing complex infrastructure.

If your credentials or resource group are not configured yet, run the `azure-project-setup` skill first.

## Choose Your Deployment Method

1. **Deploy Local Source Code (Recommended):** Deploys direct from local source code. Under the hood, Azure CLI builds the container using Cloud Native Buildpacks or your local `Dockerfile` and registers it automatically.
2. **Deploy Container Image (Registry-based):** Pull and deploy images directly from Azure Container Registry (ACR) or Docker Hub.

---

## Method 1: Deploying Direct from Local Source (`az containerapp up`)

This single command packages your local directory, builds it in the cloud, registers the image, and provisions the container app in one step.

```sh
az containerapp up \
  --name my-serverless-container \
  --resource-group my-serverless-group \
  --source . \
  --ingress external \
  --target-port 8080
```
- `--ingress external` — Opens the container to public internet requests.
- `--target-port 8080` — The port your app is listening on. Make sure your local app is configured to bind to this port (or change the value to match your code).

---

## Method 2: Deploying via Docker Registry (ACR)

If you already build images in CI, you can deploy them using Azure CLI.

### 1. Build and push to Azure Container Registry (ACR)
```sh
# Create a private registry
az acr create --resource-group my-serverless-group --name myacrserverless --sku Basic

# Build and register your image
az acr build --registry myacrserverless --image my-app:v1 .
```

### 2. Create and deploy to Container App
```sh
az containerapp create \
  --name my-serverless-container \
  --resource-group my-serverless-group \
  --image myacrserverless.azurecr.io/my-app:v1 \
  --registry-server myacrserverless.azurecr.io \
  --ingress external \
  --target-port 8080 \
  --query properties.configuration.ingress.fqdn
```

---

## Verify Deployment

When deployment is complete, ACA outputs the Fully Qualified Domain Name (FQDN) of your application. You can retrieve it at any time to verify response:

```sh
# Retrieve the public URL
URL=$(az containerapp show --name my-serverless-container --resource-group my-serverless-group --query properties.configuration.ingress.fqdn --output text)

# Verify connection
curl -fsS "https://$URL" && echo "  <- OK"
```

---

## Troubleshooting

- **`Container failed to start (crash loop)`** — The app crashed on startup. The most common cause is the container listening on a different port than specified in `--target-port`. Ensure your code matches the target-port binding.
- **`az containerapp up` command is unrecognized** — You need to enable the Container Apps CLI extension. Install it using:
  ```sh
  az extension add --name containerapp --upgrade
  ```
- **`Failed to connect to ACR`** — Ensure you have enabled admin credentials on your Azure Container Registry, or configured the active service principal to have pull permissions on the registry.
