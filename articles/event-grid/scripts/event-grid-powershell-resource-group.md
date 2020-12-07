---
title: Azure PowerShell 脚本示例 - 订阅资源组 | Microsoft Docs
description: 本文提供了一个示例 Azure PowerShell 脚本，演示如何订阅资源组的事件网格事件。
ms.devlang: powershell
ms.topic: sample
author: Johnnytechn
ms.author: v-johya
origin.date: 01/23/2020
ms.date: 12/01/2020
ms.openlocfilehash: 926ef283bc730963e9959ec3bb972adbb4ab8189
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96431847"
---
# <a name="subscribe-to-events-for-a-resource-group-with-powershell"></a>使用 PowerShell 订阅资源组的事件

此脚本创建资源组事件的事件网格订阅。

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

预览示例脚本需要事件网格模块。 若要安装，请运行 `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

## <a name="sample-script---stable"></a>示例脚本 - 稳定版

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
# Provide an endpoint for handling the events. Must be formatted "https://your-endpoint-URL"
$myEndpoint = "<your-endpoint-URL>"

# Provide the name of the resource group to create and subscribe to.
$myResourceGroup="<resource-group-name>"

# Create resource grroup
New-AzResourceGroup -Name $myResourceGroup -Location chinanorth2

# Subscribe to the resource group. Provide the name of the resource group you want to subscribe to.
New-AzEventGridSubscription `
  -Endpoint $myEndpoint `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName $myResourceGroup
```

## <a name="sample-script---preview-module"></a>示例脚本 - 预览模块

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

```powershell
# You must have the latest version of the Event Grid PowerShell module.
# To install:
# Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

# Provide an endpoint for handling the events. Must be formatted "https://your-endpoint-URL"
$myEndpoint = "<your-endpoint-URL>"

# Provide the name of the resource group to create and subscribe to.
$myResourceGroup = "<resource-group-name>"

# Create resource group
$resourceGroupID = (New-AzResourceGroup -Name $myResourceGroup -Location chinanorth2).ResourceId

# Subscribe to the resource group. Provide the name of the resource group you want to subscribe to.
New-AzEventGridSubscription `
  -ResourceId $resourceGroupID `
  -Endpoint $myEndpoint `
  -EventSubscriptionName demoSubscriptionToResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建事件订阅。 表中的每条命令链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | 创建事件网格订阅。 |

## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](../overview.md)。
* 有关 PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/get-started-azureps)。

