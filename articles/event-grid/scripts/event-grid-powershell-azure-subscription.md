---
title: Azure PowerShell - 订阅 Azure 订阅
description: 本文提供了一个示例 Azure PowerShell 脚本，演示如何订阅 Azure 订阅的事件网格事件。
ms.devlang: powershell
ms.topic: sample
author: Johnnytechn
ms.author: v-johya
origin.date: 01/23/2020
ms.date: 12/01/2020
ms.openlocfilehash: e3b97b75a84482b4d877f2068f9dd0ae515d66dc
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96431851"
---
# <a name="subscribe-to-events-for-an-azure-subscription-with-powershell"></a>使用 PowerShell 订阅 Azure 订阅的事件

此脚本创建 Azure 订阅事件的事件网格订阅。

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="sample-script---stable"></a>示例脚本 - 稳定版

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
# Provide an endpoint for handling the events. Must be formatted "https://your-endpoint-URL"
$myEndpoint = "<your-endpoint-URL>"

# Select the Azure subscription you want to subscribe to. You need this command only if the 
# current subscription is not the one you wish to subscribe to.
Set-AzContext -Subscription "<subscription-name-or-ID>"

# Subscribe to the Azure subscription. The command creates the subscription for the currently selected Azure subscription. 
New-AzEventGridSubscription -Endpoint $myEndpoint -EventSubscriptionName demoSubscriptionToAzureSub
```

## <a name="sample-script---preview-module"></a>示例脚本 - 预览模块

此预览示例脚本需要事件网格模块。 若要安装，请运行 `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

```powershell
# You must have the latest version of the Event Grid PowerShell module.
# To install:
# Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

# Provide an endpoint for handling the events. Must be formatted "https://your-endpoint-URL"
$myEndpoint = "<your-endpoint-URL>"

# Get the subscription ID
$subID = (Get-AzureRmSubscription -SubscriptionName "<subscription-name>").Id

# Subscribe to the Azure subscription. The command creates the subscription for the currently selected Azure subscription. 
New-AzureRmEventGridSubscription -ResourceId "/subscriptions/$subID" -Endpoint $myEndpoint -EventSubscriptionName demoSubscriptionToAzureSub
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建事件订阅。 表中的每条命令链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | 创建事件网格订阅。 |

## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](../overview.md)。
* 有关 PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/get-started-azureps)。

