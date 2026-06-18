---
name: azure-project-setup
description: Use when installing Azure CLI, logging in to Azure, setting an active subscription, creating a default resource group, or when the user mentions "az login", "az group create", or "azure setup".
---

# Azure Project Setup

Authenticating with Azure, configuring subscriptions, and setting up resource groups is required before provisioning or deploying any Azure serverless services.

## Installation and Authentication

1. **Install Azure CLI (`az`):**
   - Mac (Homebrew): `brew install azure-cli`
   - Linux: `curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash`
   - Windows: Run the MSI installer from Microsoft's site.

2. **Authenticate with Azure:**
   ```sh
   az login
   ```
   This will open a browser window to authenticate. If you are running in a headless or remote container environment, use device code login:
   ```sh
   az login --use-device-code
   ```

---

## Active Subscription Configuration

Multiple subscriptions may be associated with your account. Select the active subscription you want to bill:

1. **List all subscriptions:**
   ```sh
   az account list --output table
   ```

2. **Set the active subscription:**
   ```sh
   az account set --subscription "My Subscription Name or ID"
   ```

---

## Resource Group Setup

All Azure services must reside inside a **Resource Group**. Create a central group for your serverless deployment if one does not exist:

```sh
az group create \
  --name my-serverless-group \
  --location eastus
```

Confirm that the group was created successfully:
```sh
az group show --name my-serverless-group --output JSON
```

## Troubleshooting

- **`Subscription not found`** — Check your subscription name or ID for typos. Run `az account list` again to copy the exact value.
- **`az: command not found`** — The CLI isn't installed or added to your shell's system `PATH`. Restart your terminal or re-run the installation script.
- **`Expired credentials`** — If your session has expired, re-run `az login` to renew your active token.
