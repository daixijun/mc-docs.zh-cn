---
title: 使用 Azure PowerShell 部署 Azure 专用主机
description: 使用 Azure PowerShell 将 VM 部署到专用主机。
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure
origin.date: 11/12/2020
author: rockboyfor
ms.date: 01/04/2021
ms.testscope: yes
ms.testdate: 10/19/2020
ms.author: v-yeche
ms.reviewer: zivr
ms.openlocfilehash: 812fa9d793afa3d2ea7f0c8f8936ea966f108926
ms.sourcegitcommit: b4fd26098461cb779b973c7592f951aad77351f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857123"
---
<!--Verified successfully-->
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>使用 Azure PowerShell 将 VM 部署到专用主机

本文介绍了如何创建 Azure [专用主机](../dedicated-hosts.md)来托管虚拟机 (VM)。 

确保已安装 Azure PowerShell 2.8.0 或更高版本，并已使用 `Connect-AzAccount -Environment AzureChinaCloud` 登录到 Azure 帐户。 

## <a name="limitations"></a>限制

- 专用主机上目前不支持虚拟机规模集。
- 专用主机可用的大小和硬件类型因区域而异。 请参阅主机[定价页](https://www.azure.cn/pricing/details/virtual-machines/)来了解详细信息。

## <a name="create-a-host-group"></a>创建主机组

主机组是表示专用主机集合的资源。 你在某个区域中创建主机组，并向其中添加主机。 规划高可用性时，有其他选项可供选择。 你可以将以下一个或两个选项与专用主机一起使用： 

<!--Not Avaialble on availability zone-->
<!--Not Available on - Span across multiple availability zones. In this case, you are required to have a host group in each of the zones you wish to use.-->

- 跨映射到物理机架的多个容错域。 

<!--CORRECT ON in this case-->

在这种情况下，你需要为主机组提供容错域计数。 如果你不希望跨组中的容错域，请使用容错域计数 1。 

<!--Not Available on  availability zones and fault domains.-->

此示例将创建具有 2 个容错域的主机组。 

<!--Not Available on in zone 1,-->

```powershell
$rgName = "myDHResourceGroup"
$location = "ChinaEast2"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName
```

<!--CORRECT ON REMOVE    -Zone 1 -->

添加 `-SupportAutomaticPlacement true` 参数，将 VM 和规模集实例自动放置在主机组中的主机上。 如需了解详情，请参阅[手动放置与自动放置](../dedicated-hosts.md#manual-vs-automatic-placement)。

## <a name="create-a-host"></a>创建主机

现在，让我们在主机组中创建一个专用主机。 除了主机名称外，还需要提供主机的 SKU。 主机 SKU 捕获受支持的 VM 系列以及专用主机的硬件代系。

有关主机 SKU 和定价的详细信息，请参阅 [Azure 专用主机定价](https://www.azure.cn/pricing/details/virtual-machines/)。

如果为主机组设置了容错域计数，则系统会要求你为主机指定容错域。 在此示例中，我们将主机的容错域设置为 1。

```powershell
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>创建 VM

在专用主机上创建虚拟机。 

<!--Not Available on availability zone-->
<!--Not Available on If you specified an availability zone when creating your host group, you are required to use the same zone when creating the virtual machine. For this example, because our host group is in zone 1, we need to create the VM in zone 1. -->

```powershell
$cred = Get-Credential
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image Win2016Datacenter `
   -Size Standard_D4s_v3
```

<!--CORRECT ON REMOVE    -Zone 1 -->

> [!WARNING]
> 如果在没有足够资源的主机上创建虚拟机，则虚拟机将创建为“失败”状态。 

## <a name="check-the-status-of-the-host"></a>检查主机的状态

可以结合 `-InstanceView` 参数使用 [GetAzHost](https://docs.microsoft.com/powershell/module/az.compute/get-azhost) 来查看主机运行状况以及你还可以将多少虚拟机部署到主机。

```powershell
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

输出类似于以下内容：

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           : 
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    : 
    AllocatableVMs[0]  : 
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  : 
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  : 
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  : 
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  : 
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  : 
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  : 
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  : 
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  : 
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  : 
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          : 
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          : 
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    : 
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : chinaeast2
Tags                   : {}
```

## <a name="create-a-scale-set"></a>创建规模集 

部署规模集时，需要指定主机组。

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "ChinaEast2" `
  -VMScaleSetName "myDHScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"`
  -HostGroupId $hostGroup.Id
```

如果要手动选择要将规模集部署到哪个主机，请添加 `--host` 和主机名称。

## <a name="add-an-existing-vm"></a>添加现有 VM 

可将现有 VM 添加到专用主机，但必须先停止/解除分配该 VM。 在将 VM 移动到专用主机之前，请确保 VM 配置受支持：

- VM 大小必须属于专用主机所用的同一大小系列。 例如，如果专用主机是 DSv3，则 VM 大小可以是 Standard_D4s_v3，但不能是 Standard_A4_v2。 
- VM 需要位于专用主机所在的同一区域。
- VM 不能是邻近放置组的一部分。 在将 VM 移动到专用主机之前，请先从邻近放置组中删除该 VM。 有关详细信息，请参阅[将 VM 移出邻近放置组](./proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
- VM 不能位于可用性集中。

<!--Not Available on - If the VM is in an availability zone, it must be the same availability zone as the host group. The availability zone settings for the VM and the host group must match.-->

将变量值替换为你自己的信息。

```powershell
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName

$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName

$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force

Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug

Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```

## <a name="clean-up"></a>清理

即使没有部署虚拟机，也会对专用主机收费。 你应删除当前未使用的任何主机以节省成本。  

只有当不再有虚拟机使用主机时，才能删除该主机。 使用 [Remove-AzVM](https://docs.microsoft.com/powershell/module/az.compute/remove-azvm) 删除 VM。

```powershell
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

删除 VM 后，可以使用 [Remove-AzHost](https://docs.microsoft.com/powershell/module/az.compute/remove-azhost) 删除主机。

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

删除所有主机后，可以使用 [Remove-AzHostGroup](https://docs.microsoft.com/powershell/module/az.compute/remove-azhostgroup) 删除主机组。 

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

还可以使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) 通过单条命令删除整个资源组。 这会删除在组中创建的所有资源，包括所有 VM、主机和主机组。

```powershell
Remove-AzResourceGroup -Name $rgName
```

## <a name="next-steps"></a>后续步骤

- [此处](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)有一个示例模板，该模板使用区域和容错域来最大限度地提高在某个地区的复原能力。

- 也可以使用 [Azure 门户](../dedicated-hosts-portal.md)专用主机。

<!-- Update_Description: update meta properties, wording update, update link -->