---
title: Azure CLI 脚本示例 - 创建自定义主题 | Microsoft Docs
description: 本文提供了一个示例 Azure CLI 脚本，演示如何创建 Azure 事件网格自定义主题。
ms.devlang: azurecli
ms.topic: sample
author: Johnnytechn
ms.author: v-johya
origin.date: 01/23/2020
ms.date: 12/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3037dc3382f701812eb14e27daedefd9b485cc5b
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96431586"
---
# <a name="create-event-grid-custom-topic-with-azure-cli"></a>使用 Azure CLI 创建事件网格自定义主题

此脚本创建事件网格自定义主题。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="sample-script"></a>示例脚本

```azurecli
#!/bin/bash

# Give your custom topic a unique name
myTopic=demoContosoTopic

# Provice name for resource group
myResourceGroup=demoResourceGroup

# Select the Azure subscription to contain the custom topic.
az account set --subscription "<name or ID of the subscription>"

# Create resource group
az group create --name $myResourceGroup --location chinanorth2

# Create custom topic
az eventgrid topic create --resource-group $myResourceGroup --name $myTopic --location chinanorth2

# Retrieve endpoint and key to use when publishing to the topic
endpoint=$(az eventgrid topic show --name $myTopic -g $myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $myTopic -g $myResourceGroup --query "key1" --output tsv)

echo $endpoint
echo $key
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建自定义主题。 表中的每条命令链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az eventgrid topic create](https://docs.microsoft.com/cli/azure/eventgrid/topic#az-eventgrid-topic-create) | 创建事件网格自定义主题。 |


## <a name="next-steps"></a>后续步骤

* 有关查询订阅的信息，请参阅[查询事件网格订阅](../query-event-subscriptions.md)。
* 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli)。

