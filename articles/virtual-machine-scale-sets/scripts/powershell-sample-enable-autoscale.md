---
title: Azure PowerShell 示例 - 启用基于主机的自动缩放
description: 此脚本创建运行 Windows Server 2016 的虚拟机规模集，当 CPU 负载发生变化时，它会使用基于主机的指标自动缩放。
author: ju-shim
ms.author: v-junlch
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 08/07/2020
ms.custom: avverma
ms.openlocfilehash: 978bf9a14cd5b6c6e2bebd8bb348939eac325155
ms.sourcegitcommit: f436acd1e2a0108918a6d2ee9a1aac88827d6e37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509045"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-powershell"></a>使用 PowerShell 自动缩放虚拟机规模集
此脚本创建运行 Windows Server 2016 的虚拟机规模集，当 CPU 负载发生变化时，它会使用基于主机的指标自动缩放。

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>示例脚本


```powershell
# Define the resource group name and location
$mySubscriptionId = (Get-AzSubscription)[0].Id
$myResourceGroup = "myResourceGroup"
$myScaleSet = "myScaleSet"
$myLocation = "China North"

# Provide your own secure password for use with the VM instances
$cred = Get-Credential

# Create a virtual machine scale set and supporting resources
# A resource group, virtual network, load balancer, and NAT rules are automatically
# created if they do not already exist
New-AzVmss `
  -ResourceGroupName $myResourceGroup `
  -VMScaleSetName $myScaleSet `
  -Location $myLocation `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -Credential $cred

# Create an autoscale rule to increase the number of VM instances by 3 when the average CPU load over a 5-minute window
# is greater than 70%
$myRuleScaleOut = New-AzAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic "Average" `
  -TimeWindow 00:05:00 `
  -Operator "GreaterThan" `
  -Threshold 70 `
  -ScaleActionDirection "Increase" `
  â€“ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 3 `
  -ScaleActionCooldown 00:05:00

# Create an autoscale rule to decrease the number of VM instances by 1 when the average CPU load over a 5-minute window
# is less than 30%
$myRuleScaleIn = New-AzAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator "LessThan" `
  -MetricStatistic "Average" `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection "Decrease" `
  â€“ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 1

# Create the autoscale profle that includes the scale out and scale in rules
$myScaleProfile = New-AzAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"

# Apply the autoscale profile to the virtual machine scale set
Add-AzAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfile $myScaleProfile
```

## <a name="clean-up-deployment"></a>清理部署
运行以下命令可删除资源组、规模集和所有相关资源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明
此脚本使用以下命令创建部署。 表中的每一项均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) | 创建虚拟机规模集和所有支持资源，包括虚拟网络、负载均衡器和 NAT 规则。 |
| [Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) | 获取有关虚拟机规模集的信息。 |
| [Add-AzVmssExtension](https://docs.microsoft.com/powershell/module/az.compute/add-azvmssextension) | 添加自定义脚本的 VM 扩展以安装基本的 Web 应用程序。 |
| [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss) | 更新虚拟机规模集模型以应用 VM 扩展。 |
| [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) | 获取有关负载均衡器使用的分配公共 IP 地址的信息。 |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组及其中包含的所有资源。 |

## <a name="next-steps"></a>后续步骤
有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/)。


