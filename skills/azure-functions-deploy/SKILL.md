---
name: azure-functions-deploy
description: Use when deploying a serverless Function App to Microsoft Azure, or when the user mentions "Azure Functions", "az functionapp", "func azure", "functionapp publish", or serverless functions on Azure. Covers zip publishing, core tools, and CLI commands.
---

# Deploy to Azure Functions

Azure Functions is Microsoft's primary event-driven serverless compute platform. It runs small blocks of code (HTTP APIs, timers, queue handlers) scaling to zero on the Consumption Plan.

If your credentials or resource group are not configured yet, run the `azure-project-setup` skill first.

## Prerequisites

To package and deploy functions locally, install **Azure Functions Core Tools**:
- Mac (Homebrew): `brew tap azure/functions && brew install azure-functions-core-tools@4`
- npm (global): `npm install -g azure-functions-core-tools@4 --unsafe-perm`

---

## Method 1: Deploying via Azure Functions Core Tools (Recommended)

This is the easiest, most reliable way to compile and publish function apps locally.

### 1. Initialize a Local Project (if from scratch)
```sh
# Initialize in current directory (e.g. Node worker)
func init . --javascript

# Add an HTTP-triggered function
func new --name hello --template "HTTP trigger" --authlevel "anonymous"
```

### 2. Create the Azure Infrastructure (First-time setup)
Azure Functions requires a Storage Account and a Function App hosting instance.
```sh
# Create a storage account
az storage account create \
  --name mystorages8sskills \
  --location eastus \
  --resource-group my-serverless-group \
  --sku Standard_LRS

# Create the Function App on Consumption Plan
az functionapp create \
  --name my-serverless-funcapp \
  --storage-account mystorages8sskills \
  --consumption-plan-location eastus \
  --resource-group my-serverless-group \
  --functions-version 4 \
  --runtime node
```

### 3. Build & Deploy
Deploy the code directly from your project root:
```sh
func azure functionapp publish my-serverless-funcapp
```
This zips the local repository, uploads it to Azure, and activates the endpoints.

---

## Verify Deployment

When publishing completes, the command outputs the public endpoint URL. 
You can retrieve your function details and verify that it serves:

```sh
# List active functions and URLs
az functionapp function list \
  --name my-serverless-funcapp \
  --resource-group my-serverless-group \
  --query "[].[name, config.bindings[0].route, href]" \
  --output table

# Verify request
URL="https://my-serverless-funcapp.azurewebsites.net/api/hello"
curl -fsS "$URL" && echo "  <- OK"
```

---

## Troubleshooting

- **`ResourceNotFound` on deploy** — The Function App name is misspelled, or it belongs to a different resource group than specified. Verify name matches the infrastructure created.
- **`func command not found`** — Azure Functions Core Tools are not installed. Re-run installation or try deploying a zipped package directly via Azure CLI:
  ```sh
  zip -r function.zip .
  az functionapp deployment source config-zip \
    --name my-serverless-funcapp \
    --resource-group my-serverless-group \
    --src function.zip
  ```
- **`502 Bad Gateway` on HTTP endpoints** — The node runtime is crashing. Check your runtime versions (e.g. Node 18 locally vs Node 16 in Azure configuration) and look at live stream logs:
  ```sh
  az functionapp log tail --name my-serverless-funcapp --resource-group my-serverless-group
  ```
