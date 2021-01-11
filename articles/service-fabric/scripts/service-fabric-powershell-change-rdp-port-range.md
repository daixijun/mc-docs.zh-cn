---
title: Azure PowerShell 脚本示例 - 更改 RDP 端口范围 | Azure
description: Azure PowerShell 脚本示例 - 更改已部署群集的 RDP 端口范围。
services: service-fabric
tags: azure-service-management
author: rockboyfor
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
origin.date: 03/19/2018
ms.date: 01/11/2021
ms.author: v-yeche
ms.openlocfilehash: e3189631a07270d157a3b6412f7a30215909b567
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98022740"
---
# <a name="update-the-rdp-port-range-values"></a>更新 RDP 端口范围值

部署群集后，此示例脚本可更改群集节点 VM 上的 RDP 端口范围值。  使用了 Azure PowerShell，使底层 VM 不会重启。  该脚本会获取群集资源组中的 `Microsoft.Network/loadBalancers` 资源，并更新 `inboundNatPools.frontendPortRangeStart` 和 `inboundNatPools.frontendPortRangeEnd` 值。 根据需要自定义参数。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

必要时，请使用 [Azure PowerShell 指南](https://docs.microsoft.com/powershell/azure/)中的说明安装 Azure PowerShell。

## <a name="sample-script"></a>示例脚本

```powershell
Connect-AzAccount -Environment AzureChinaCloud
Get-AzSubscription
Set-AzContext -SubscriptionId 'yourSubscriptionId'

$groupname = "mysfclustergroup"
$start=3400
$end=4400

# Get the load balancer resource
$resource = Get-AzResource | Where {$_.ResourceGroupName -eq $groupname -and $_.ResourceType -eq "Microsoft.Network/loadBalancers"} 
$lb = Get-AzResource -ResourceGroupName $groupname -ResourceType Microsoft.Network/loadBalancers -ResourceName $resource.Name

# Update the front end port range
$lb.Properties.inboundNatPools.properties.frontendPortRangeStart = $start
$lb.Properties.inboundNatPools.properties.frontendPortRangeEnd = $end

# Write the inbound NAT pools properties
Write-Host ($lb.Properties.inboundNatPools | Format-List | Out-String)

# Update the load balancer
Set-AzResource -PropertyObject $lb.Properties -ResourceGroupName $groupname -ResourceType Microsoft.Network/loadBalancers -ResourceName $lb.name  -Force

```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | 获取 `Microsoft.Network/loadBalancers` 资源。 |
|[Set-AzResource](https://docs.microsoft.com/powershell/module/az.resources/set-azresource)|更新 `Microsoft.Network/loadBalancers` 资源。|

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/)。

可以在 [Azure PowerShell 示例](../service-fabric-powershell-samples.md)中找到 Azure Service Fabric 的其他 Azure Powershell 示例。

<!-- Update_Description: update meta properties, wording update, update link -->