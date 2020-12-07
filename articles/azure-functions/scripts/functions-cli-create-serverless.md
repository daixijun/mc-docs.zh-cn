---
title: 使用 Azure CLI 创建无服务器函数应用
description: 使用 Azure CLI 在 Azure 中创建可无服务器执行的函数应用。
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.topic: sample
ms.date: 11/18/2020
ms.author: v-junlch
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 14a22585dc39b4f795ad30c81a4ac2c9806f1a2a
ms.sourcegitcommit: f436acd1e2a0108918a6d2ee9a1aac88827d6e37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509040"
---
# <a name="create-a-function-app-for-serverless-code-execution"></a>创建适合无服务器代码执行的函数应用 

此 Azure Functions 示例脚本将创建一个函数应用，作为函数的容器。 将使用[消耗计划](../functions-scale.md#consumption-plan)创建最适合事件驱动无服务器工作负荷的函数应用。

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0 或更高版本。 

## <a name="sample-script"></a>示例脚本

此脚本使用[消耗计划](../functions-scale.md#consumption-plan)创建 Azure 函数应用。

```azurecli
#!/bin/bash

# Function app and storage account names must be unique.
storageName=mystorageaccount$RANDOM
functionAppName=myserverlessfunc$RANDOM
region=chinanorth2

# Create a resource group.
az group create --name myResourceGroup --location $region

# Create an Azure storage account in the resource group.
az storage account create \
  --name $storageName \
  --location $region \
  --resource-group myResourceGroup \
  --sku Standard_LRS

# Create a serverless function app in the resource group.
az functionapp create \
  --name $functionAppName \
  --storage-account $storageName \
  --consumption-plan-location $region \
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
| [az functionapp create](/cli/functionapp#az-functionapp-create) | 创建 Function App。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli)。

可以在 [Azure Functions 文档](../functions-cli-samples.md)中找到其他 Azure Functions CLI 脚本示例。

