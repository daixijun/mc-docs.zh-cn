---
title: Azure PowerShell - 订阅 Blob 存储帐户
description: 本文提供了一个示例 Azure PowerShell 脚本，演示如何订阅 Blob 存储帐户的事件网格事件。
ms.devlang: powershell
ms.topic: sample
author: Johnnytechn
ms.author: v-johya
origin.date: 01/23/2020
ms.date: 12/01/2020
ms.openlocfilehash: 95dbf34576dc680634153769ec090cc2ee3c35c0
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96431850"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-powershell"></a>使用 PowerShell 订阅 Blob 存储帐户的事件

此脚本创建 Blob 存储帐户事件的事件网格订阅。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="sample-script---stable"></a>示例脚本 - 稳定版

```powershell
# Provide a unique name for the Blob storage account.
$storageName = "<your-unique-storage-name>"

# Provide an endpoint for handling the events. Must be formatted "https://your-endpoint-URL"
$myEndpoint = "<your-endpoint-URL>"

# Provide the name of the resource group to create. It will contain the storage account.
$myResourceGroup="<resource-group-name>"

# Create resource group
New-AzResourceGroup -Name $myResourceGroup -Location chinanorth2

# Create the Blob storage account. 
New-AzStorageAccount -ResourceGroupName $myResourceGroup `
  -Name $storageName `
  -Location chinanorth2 `
  -SkuName Standard_LRS `
  -Kind BlobStorage `
  -AccessTier Hot

# Get the resource ID of the Blob storage account.
$storageId = (Get-AzStorageAccount -ResourceGroupName $myResourceGroup -AccountName $storageName).Id

# Subscribe to the Blob storage account. 
New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint $myEndpoint `
  -ResourceId $storageId
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建事件订阅。 表中的每条命令链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | 创建事件网格订阅。 |

## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](../overview.md)。
* 有关 PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/get-started-azureps)。

