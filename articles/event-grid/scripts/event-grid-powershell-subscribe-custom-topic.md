---
title: Azure PowerShell 脚本示例 - 订阅自定义主题 | Microsoft Docs
description: 本文提供了一个示例 Azure PowerShell 脚本，演示如何订阅自定义主题的事件网格事件。
ms.devlang: powershell
ms.topic: sample
author: Johnnytechn
ms.author: v-johya
origin.date: 01/23/2020
ms.date: 12/01/2020
ms.openlocfilehash: b3c5389e4df6d1d627e127a0efc43b6912293ff7
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96431844"
---
# <a name="subscribe-to-events-for-a-custom-topic-with-powershell"></a>使用 PowerShell 订阅自定义主题的事件

此脚本创建自定义主题事件的事件网格订阅。

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

预览示例脚本需要事件网格模块。 若要安装，请运行 `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

## <a name="sample-script---stable"></a>示例脚本 - 稳定版

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
# Provide the name of the topic you are subscribing to
$myTopic = "<your-custom-topic-name>"

# Provide an endpoint for handling the events. Must be formatted "https://your-endpoint-URL"
$myEndpoint = "<your-endpoint-URL>"

# Provide a name for resource group to create. It will contain the custom event.
$myResourceGroup = "<resource-group-name>"

# Create resource group
New-AzResourceGroup -Name $myResourceGroup -Location chinanorth2

# Create custom topic
New-AzEventGridTopic -ResourceGroupName $myResourceGroup -Name $myTopic -Location chinanorth2 

# Subscribe to the custom event. Include the resource group that contains the custom topic.
New-AzEventGridSubscription `
  -EventSubscriptionName demoSubscription `
  -Endpoint $myEndpoint `
  -ResourceGroupName $myResourceGroup `
  -TopicName $myTopic
```

## <a name="sample-script---preview-module"></a>示例脚本 - 预览模块

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

```powershell
# You must have the latest version of the Event Grid PowerShell module.
# To install:
# Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

# Provide the name of the topic you are subscribing to
$myTopic = "<your-custom-topic-name>"

# Provide an endpoint for handling the events. Must be formatted "https://your-endpoint-URL"
$myEndpoint = "<your-endpoint-URL>"

# Provide the name of the resource group to create. It will contain the custom topic.
$myResourceGroup = "<resource-group-name>"

# Create resource group
New-AzResourceGroup -Name $myResourceGroup -Location chinanorth2

# Create custom topic and get its resource ID.
$topicID = (New-AzEventGridTopic -ResourceGroupName $myResourceGroup -Name $myTopic -Location chinanorth2).Id 

# Subscribe to the custom event. Include the resource group that contains the custom topic.
New-AzEventGridSubscription `
  -ResourceId $topicID `
  -EventSubscriptionName demoSubscription `
  -Endpoint $myEndpoint 
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建事件订阅。 表中的每条命令链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | 创建事件网格订阅。 |

## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](../overview.md)。
* 有关 PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/get-started-azureps)。

