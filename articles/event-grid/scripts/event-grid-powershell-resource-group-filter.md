---
title: Azure PowerShell - 订阅资源组
description: 本文提供了一个示例 Azure PowerShell 脚本，演示如何订阅资源组的事件网格事件，以及如何筛选资源。
ms.devlang: powershell
ms.topic: sample
author: Johnnytechn
ms.author: v-johya
origin.date: 01/23/2020
ms.date: 12/01/2020
ms.openlocfilehash: d6f31f19cb5fa002f2ec662c0f51977086fb32e3
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96431849"
---
# <a name="subscribe-to-events-for-a-resource-group-and-filter-for-a-resource-with-powershell"></a>使用 PowerShell 订阅资源组的事件并筛选出资源

此脚本创建资源组事件的事件网格订阅。 它使用筛选器，只获取资源组中指定资源的事件。

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="sample-script---stable"></a>示例脚本 - 稳定版

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
# Provide an endpoint for handling the events. Must be formatted "https://your-endpoint-URL"
$myEndpoint = "<your-endpoint-URL>"

# Provide the name of the resource group to create. It will contain the network security group.
# You will subscribe to events for this resource group.
$myResourceGroup = "<resource-group-name>"

# Provide a name for the network security group to create.
$nsgName = "<your-nsg-name>"

# Create the resource group
New-AzResourceGroup -Name $myResourceGroup -Location chinanorth2

# Create a network security group. You will filter events to only those that are related to this resource.
New-AzNetworkSecurityGroup -Name $nsgName -ResourceGroupName $myResourceGroup  -Location chinanorth2

# Get the resource ID to filter events. The name of the network security group must not be the same as the other resource names.
$resourceId = (Get-AzResource -ResourceName $nsgName -ResourceGroupName $myResourceGroup).ResourceId

# Subscribe to the resource group. Provide the name of the resource group you want to subscribe to.
New-AzEventGridSubscription `
  -Endpoint $myEndpoint `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName $myResourceGroup `
  -SubjectBeginsWith $resourceId
```

## <a name="sample-script---preview-module"></a>示例脚本 - 预览模块

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

预览示例脚本需要事件网格模块。 若要安装，请运行 `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

```powershell
# You must have the latest version of the Event Grid PowerShell module.
# To install:
# Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

# Provide an endpoint for handling the events. Must be formatted "https://your-endpoint-URL"
$myEndpoint = "<your-endpoint-URL>"

# Provide the name of the custom topic to create
$topicName = "<your-topic-name>"

# Provide the name of the resource group to create. It will contain the custom topic.
$myResourceGroup= "<resource-group-name>"

# Create the resource group
New-AzResourceGroup -Name $myResourceGroup -Location chinanorth2

# Create custom topic
New-AzEventGridTopic -ResourceGroupName $myResourceGroup -Location chinanorth2 -Name $topicName

# Get resource ID of custom topic
$topicid = (Get-AzEventGridTopic -ResourceGroupName $myResourceGroup -Name $topicName).Id

# Set the operator type, field and values for the filtering
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

# Subscribe to the custom topic. Filter based on a value in the event data.
New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName demoSubWithFilter `
  -Endpoint $myEndpoint `
  -AdvancedFilter @($AdvFilter1)
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建事件订阅。 表中的每条命令链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | 创建事件网格订阅。 |

## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](../overview.md)。
* 有关 PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/get-started-azureps)。

