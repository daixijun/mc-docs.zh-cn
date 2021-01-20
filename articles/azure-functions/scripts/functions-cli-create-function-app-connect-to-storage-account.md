---
title: 使用连接的存储创建函数应用 - Azure CLI
description: Azure CLI 脚本示例 - 创建用于连接到 Azure 存储的 Azure Function
ms.topic: sample
ms.date: 01/13/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7a984565e44dccdf72f6fbba69f45c56db7a85cd
ms.sourcegitcommit: 88173d1dae28f89331de5f877c5b3777927d67e4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98195252"
---
# <a name="create-a-function-app-with-a-named-storage-account-connection"></a>创建具有命名存储帐户连接的函数应用 

此 Azure Functions 示例脚本先创建一个函数应用，然后将该函数连接到 Azure 存储帐户。 创建的应用设置（包含连接）可以与[存储触发器或绑定](../functions-bindings-storage-blob.md)配合使用。 

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0 或更高版本。 

## <a name="sample-script"></a>示例脚本

此示例创建 Azure Function app，并将存储连接字符串添加到应用设置。

```azurecli
#!/bin/bash

# Function app and storage account names must be unique.
storageName="mystorageaccount$RANDOM"
functionAppName="myfuncwithstorage$RANDOM"
region=chinanorth2

# Create a resource group with location.
az group create \
  --name myResourceGroup \
  --location $region

# Create a storage account in the resource group.
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

# Get the storage account connection string. 
connstr=$(az storage account show-connection-string --name $storageName --resource-group myResourceGroup --query connectionString --output tsv)

# Update function app settings to connect to the storage account.
az functionapp config appsettings set \
  --name $functionAppName \
  --resource-group myResourceGroup \
  --settings StorageConStr=$connstr
```

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/group#az-group-create) | 使用相关位置创建资源组。 |
| [az storage account create](/cli/storage/account#az-storage-account-create) | 创建存储帐户。 |
| [az functionapp create](/cli/functionapp#az-functionapp-create) | 在无服务器[消耗计划](../consumption-plan.md)中创建函数应用。 |
| [az storage account show-connection-string](/cli/storage/account#az-storage-account-show-connection-string) | 获取帐户的连接字符串。 |
| [az functionapp config appsettings set](/cli/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | 将连接字符串设置为函数应用中的应用设置。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli)。

可以在 [Azure Functions 文档](../functions-cli-samples.md)中找到其他 Azure Functions CLI 脚本示例。

