---
title: Azure PowerShell 脚本示例 - 创建自定义主题 | Microsoft Docs
description: 本文提供了一个示例 Azure PowerShell 脚本，演示如何创建事件网格自定义主题。
services: event-grid
documentationcenter: na
author: Johnnytechn
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 01/23/2020
ms.date: 12/01/2020
ms.author: v-johya
ms.openlocfilehash: d209d806802eebc3a7cb5ab5dbcf0c3f4b1f4f28
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96431848"
---
# <a name="create-event-grid-custom-topic-with-powershell"></a>使用 PowerShell 创建事件网格自定义主题

此脚本创建事件网格自定义主题。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="sample-script"></a>示例脚本

```powershell
# Give your custom topic a unique name
$myTopic = "<your-custom-topic-name>"

# Provide a name for resource group to create. It will contain the custom event.
$myResourceGroup = "<resource-group-name>"

# Create resource group
New-AzResourceGroup -Name $myResourceGroup -Location chinanorth2

# Create custom topic
New-AzEventGridTopic -ResourceGroupName $myResourceGroup -Name $myTopic -Location chinanorth2 

# Retrieve endpoint and key to use when publishing to the topic
$endpoint = (Get-AzEventGridTopic -ResourceGroupName $myResourceGroup -Name $myTopic).Endpoint
$key = (Get-AzEventGridTopicKey -ResourceGroupName $myResourceGroup -Name $myTopic).Key1

$endpoint
$key
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建自定义主题。 表中的每条命令链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [New-AzEventGridTopic](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridtopic) | 创建事件网格自定义主题。 |

## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](../overview.md)。
* 有关 PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/get-started-azureps)。

