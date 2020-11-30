---
title: 创建连接到 Azure Cosmos DB 的函数应用 - Azure CLI
description: Azure CLI 脚本示例 - 创建用于连接到 Azure Cosmos DB 的 Azure Function
ms.topic: sample
ms.date: 11/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 273c29e3ce589ec4f536232569a49a659135db49
ms.sourcegitcommit: b072689d006cbf9795612acf68e2c4fee0eccfbc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "95970744"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>创建用于连接到 Azure Cosmos DB 的 Azure Function

此 Azure Functions 示例脚本先创建一个函数应用，然后将该函数连接到 Azure Cosmos DB 数据库。 创建的应用设置（包含连接）可以与 [Azure Cosmos DB 触发器或绑定](../functions-bindings-cosmosdb.md)配合使用。

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0 或更高版本。 

## <a name="sample-script"></a>示例脚本

此示例创建 Azure Function app，并将 Cosmos DB 终结点和访问密钥添加到应用设置。

```azurecli
#!/bin/bash

# Function app and storage account names must be unique.
storageName=mystorageaccount$RANDOM
functionAppName="myfuncwithcosmosdb$RANDOM"
region=chinanorth2

# Create a resource group with location.
az group create \
  --name myResourceGroup \
  --location $region

# Create a storage account for the function app. 
az storage account create \
  --name $storageName \
  --location $region \
  --resource-group myResourceGroup \
  --sku Standard_LRS

# Create a serverless function app in the resource group.
az functionapp create \
  --name $functionAppName \
  --resource-group myResourceGroup \
  --storage-account $storageName \
  --consumption-plan-location $region \
  --functions-version 2

# Create an Azure Cosmos DB database using the same function app name.
az cosmosdb create \
  --name $functionAppName \
  --resource-group myResourceGroup

# Get the Azure Cosmos DB connection string.
endpoint=$(az cosmosdb show \
  --name $functionAppName \
  --resource-group myResourceGroup \
  --query documentEndpoint \
  --output tsv)

key=$(az cosmosdb list-keys \
  --name $functionAppName \
  --resource-group myResourceGroup \
  --query primaryMasterKey \
  --output tsv)

# Configure function app settings to use the Azure Cosmos DB connection string.
az functionapp config appsettings set \
  --name $functionAppName \
  --resource-group myResourceGroup \
  --setting CosmosDB_Endpoint=$endpoint CosmosDB_Key=$key
```

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令：表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az group create](/cli/group#az-group-create) | 使用相关位置创建资源组 |
| [az storage accounts create](/cli/storage/account#az-storage-account-create) | 创建存储帐户 |
| [az functionapp create](/cli/functionapp#az-functionapp-create) | 在无服务器[消耗计划](../functions-scale.md#consumption-plan)中创建函数应用。 |
| [az cosmosdb create](/cli/cosmosdb#az-cosmosdb-create) | 创建 Azure Cosmos DB 数据库。 |
| [az cosmosdb show](/cli/cosmosdb#az-cosmosdb-show)| 获取数据库帐户连接。 |
| [az cosmosdb list-keys](/cli/cosmosdb#az-cosmosdb-list-keys)| 获取数据库密钥。 |
| [az functionapp config appsettings set](/cli/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | 将连接字符串设置为函数应用中的应用设置。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli)。

可以在 [Azure Functions 文档](../functions-cli-samples.md)中找到其他 Azure Functions CLI 脚本示例。





