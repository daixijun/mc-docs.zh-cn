---
title: 在应用服务计划中创建 Function App - Azure CLI
description: Azure CLI 脚本示例 - 在应用服务计划中创建 Function App
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.topic: sample
ms.date: 11/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 69d4a050ee2635e1f36dd49377f72fa18f8cee1b
ms.sourcegitcommit: f436acd1e2a0108918a6d2ee9a1aac88827d6e37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96508726"
---
# <a name="create-a-function-app-in-an-app-service-plan"></a>在应用服务计划中创建 Function App

此 Azure Functions 示例脚本将创建一个函数应用，作为函数的容器。 创建的函数应用使用专用应用服务计划，这意味着服务器资源将始终启用。

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0 或更高版本。

## <a name="sample-script"></a>示例脚本

此脚本使用专用[应用服务计划](../functions-scale.md#app-service-plan)创建 Azure Function App。

```azurecli
#!/bin/bash

# Function app and storage account names must be unique.
storageName=mystorageaccount$RANDOM
functionAppName=myappsvcpfunc$RANDOM
region=chinanorth2

# Create a resource resourceGroupName
az group create \
  --name myResourceGroup \
  --location $region

# Create an azure storage account
az storage account create \
  --name $storageName \
  --location $region \
  --resource-group myResourceGroup \
  --sku Standard_LRS

# Create an App Service plan
az functionapp plan create \
  --name myappserviceplan \
  --resource-group myResourceGroup \
  --location $region \
  --sku B1

# Create a Function App
az functionapp create \
  --name $functionAppName \
  --storage-account $storageName \
  --plan myappserviceplan \
  --resource-group myResourceGroup \
  --functions-version 2
  
```

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

表中的每条命令均链接到特定于命令的文档。 此脚本使用以下命令：

| 命令 | 说明 |
|---|---|
| [az group create](/cli/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az storage account create](/cli/storage/account#az-storage-account-create) | 创建 Azure 存储帐户。 |
| [az functionapp plan create](/cli/functionapp/plan#az-functionapp-plan-create) | 创建高级计划。 |
| [az functionapp create](/cli/functionapp#az-functionapp-create) | 在应用服务计划中创建函数应用。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli)。

可以在 [Azure Functions 文档](../functions-cli-samples.md)中找到其他 Azure Functions CLI 脚本示例。

