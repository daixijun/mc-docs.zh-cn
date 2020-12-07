---
title: Azure CLI 脚本示例 - 订阅 Blob 存储帐户 | Microsoft Docs
description: 本文提供了一个示例 Azure CLI 脚本，演示如何订阅 Azure Blob 存储帐户的事件。
ms.devlang: azurecli
ms.topic: sample
author: Johnnytechn
ms.author: v-johya
origin.date: 01/23/2020
ms.date: 12/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: ce2d21b8076cbbd4e2c48fdf9e4b50429ece0e5f
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96431587"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-azure-cli"></a>使用 Azure CLI 订阅 Blob 存储帐户的事件

此脚本创建 Blob 存储帐户事件的事件网格订阅。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="sample-script"></a>示例脚本

```azurecli
#!/bin/bash

# You must have the latest version of the Azure CLI

# Provide a unique name for the Blob storage account.
storageName="<Name of the storage account>"

# Provide an endpoint for handling the events.
myEndpoint="<endpoint URL>"

# Provide the name of the resource group to contain the storage account.
myResourceGroup="<resource group name>"

# Select the Azure subscription to contain the storage account and event subscription.
az account set --subscription "<name or ID of the subscription>"

# Create resource group
az group create --name $myResourceGroup --location chinanorth2

# Create the Blob storage account. 
az storage account create \
  --name $storageName \
  --location chinanorth2 \
  --resource-group $myResourceGroup \
  --sku Standard_LRS \
  --kind BlobStorage \
  --access-tier Hot

# Get the resource ID of the Blob storage account.
storageid=$(az storage account show --name $storageName --resource-group $myResourceGroup --query id --output tsv)

# Subscribe to the Blob storage account. 
az eventgrid event-subscription create \
  --source-resource-id $storageid \
  --name demoSubToStorage \
  --endpoint $myEndpoint
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建事件订阅。 表中的每条命令链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | 创建事件网格订阅。 |
| [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) - 扩展版本 | 创建事件网格订阅。 |

## <a name="next-steps"></a>后续步骤

* 有关查询订阅的信息，请参阅[查询事件网格订阅](../query-event-subscriptions.md)。
* 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli)。

