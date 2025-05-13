---
title: Azure Cloud DevOps
date: 2025-05-12
modify_date: 2025-05-12
tags: Azure Cloud DevOps
key: Azure-Cloud-2025-05-12
---

## Azure CLI

### Install Azure CLI

- [How to install the Azure CLI - EN](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
- [How to install the Azure CLI - CN](https://learn.microsoft.com/zh-cn/cli/azure/install-azure-cli?view=azure-cli-latest)


## Azure CLI Commands

- [Azure CLI reference](https://learn.microsoft.com/en-us/cli/azure/reference-index?view=azure-cli-latest)

### Azure login

Get available Azure cloud

```bash
az cloud list -o table
```

<!--more-->

Switch to Azure China cloud

```bash
az cloud set --name AzureChinaCloud
```

Login to Azure

```bash
az login

# To see if logged in
az account list
az account show
az account get-access-token
```

Get available Azure cloud locations:

```bash
az account list-locations -o table

# Get name only
az account list-locations --query "[].{Name:name}" -o table

# Get more details
az account list-locations --query "[].{Name:name, DisplayName:displayName, PhysicalLocation:metadata.physicalLocation}" -o table
```

### Azure resource info

Get available Azure resource groups:

```bash
az group list -o table

# Get available Azure resource groups in a specific location:
az group list --query "[?location=='chinanorth3']" -o table
```

Get available Azure SKUs:

```bash
az vm list-skus -o table

# Get specific SKUs in a specific location
az vm list-skus --resource-type virtualMachines -l chinanorth3 -o table

az postgres flexible-server list-skus -l chinanorth3 -o table

az mysql flexible-server list-skus -l chinanorth3 -o table
```

### Azure resource operations

#### VM operations

```bash
# Get VM list
az vm list -o table

# Get VM details
az vm list -d -o table
```

Get Azure Redis Cache:

```bash
az redis list

# Get hostname only
az redis list --query "[].{HostName:hostName}" -o table

# Get more details
az redis list --query "[].{HostName:hostName, Port:port, SslPort:sslPort, Version:redisVersion}" -o table
```

Get Azure PostgreSQL Flexible Server:

```bash
az postgres flexible-server list

# Get fully qualified domain name
az postgres flexible-server list --query "[].{FQDN:fullyQualifiedDomainName}" -o table
```

Get Azure Kubernetes Service (AKS):

```bash
az aks list -o table
```