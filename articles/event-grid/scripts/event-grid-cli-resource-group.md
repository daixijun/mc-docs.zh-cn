---
title: Azure CLI 脚本示例 - 订阅资源组 | Microsoft Docs
description: 本文提供了一个示例 Azure CLI 脚本，演示如何订阅资源组的 Azure 事件网格事件。
ms.devlang: azurecli
ms.topic: sample
author: Johnnytechn
ms.author: v-johya
origin.date: 01/23/2020
ms.date: 12/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: f345612a05af33369519ae2a341444815602dd59
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96431862"
---
# <a name="subscribe-to-events-for-a-resource-group-with-azure-cli"></a>使用 Azure CLI 订阅资源组的事件

此脚本创建资源组事件的事件网格订阅。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

预览示例脚本需要事件网格扩展。 若要安装，请运行 `az extension add --name eventgrid`。

## <a name="sample-script---stable"></a>示例脚本 - 稳定版

```azurecli
#!/bin/bash

# Provide an endpoint for handling the events.
myEndpoint="<endpoint URL>"

# Provide the name of the resource group to subscribe to.
myResourceGroup="<resource group name>"

# Select the Azure subscription that contains the resource group.
az account set --subscription "<name or ID of the subscription>"

# Get resource ID of the resource group.
resourceGroupID=$(az group show --name $myResourceGroup --query id --output tsv)

# Subscribe to the resource group. Provide the name of the resource group you want to subscribe to.
az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --source-resource-id $resourceGroupID \
  --endpoint $myEndpoint
```
<!--Correct in MC about the argument of "--source-resource-id" -->

## <a name="sample-script---preview-extension"></a>示例脚本 - 预览扩展

```azurecli
#!/bin/bash

# Provide an endpoint for handling the events.
myEndpoint="<endpoint URL>"

# Provide the name of the resource group to subscribe to.
myResourceGroup="<resource group name>"

# Select the Azure subscription that contains the resource group.
az account set --subscription "<name or ID of the subscription>"

# Get resource ID of the resource group.
resourceGroupID=$(az group show --name $myResourceGroup --query id --output tsv)

# Subscribe to the resource group. Provide the name of the resource group you want to subscribe to.
az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --source-resource-id $resourceGroupID \
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

