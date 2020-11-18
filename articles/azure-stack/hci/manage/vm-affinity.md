---
title: 使用 Windows PowerShell 设置 VM 相关性规则
description: 了解如何使用 Windows PowerShell 设置 VM 相关性规则
author: WenJason
ms.topic: how-to
origin.date: 10/14/2020
ms.date: 11/09/2020
ms.author: v-jay
ms.reviewer: JasonGerend
ms.openlocfilehash: 7189e1685bc2045844cd81fb9b97bba00b96eb82
ms.sourcegitcommit: f187b1a355e2efafea30bca70afce49a2460d0c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330497"
---
# <a name="create-server-and-site-affinity-rules-for-vms"></a>为 VM 创建服务器和站点相关性规则

> 适用于 Azure Stack HCI 版本 20H2

使用 Windows Admin Center 或 Windows PowerShell，可为群集中的虚拟机 (VM) 轻松创建相关性和反相关性规则。

相关性是在两个或更多个资源组或角色（如虚拟机 (VM)）之间建立关系的规则，用于将它们保存在同一个服务器、群集或站点上。 反相关性则相反，它用于使指定的 VM 或资源组彼此分开，例如，将两个域控制器放置在单独的服务器或单独的站点中以进行灾难恢复。

使用相关性和反相关性规则，任何群集 VM 要么保留在同一群集节点中，要么被阻止位于同一群集节点中。  这样一来，将 VM 移出节点的唯一方法是手动移出。  还可以将 VM 与其自身的存储（例如其 VHDX 所在的群集共享卷 (CSV)）保存在一起。

结合使用相关性和反相关性规则，还可配置一个跨两个站点的拉伸群集，并将 VM 保存在所需的站点中。

## <a name="using-windows-admin-center"></a>使用 Windows Admin Center

可以使用 Windows Admin Center 来创建基本的相关性和反相关性规则。

:::image type="content" source="media/vm-affinity/vm-affinity-rules.png" alt-text="虚拟机屏幕" lightbox="media/vm-affinity/vm-affinity-rules.png":::

1. 在 Windows Admin Center 主页中的“所有连接”下，选择要为其创建 VM 规则的服务器或群集。
1. 在“工具”下，选择“设置” 。
1. 在“设置”下选择“相关性规则”，然后在“相关性规则”下选择“创建规则”。
1. 在“规则名称”下，输入规则的名称。
1. 在“规则类型”下，选择“一起(同一服务器)”或“分开(不同服务器)”，以将 VM 放置在同一服务器或不同服务器上。
1. 在“适用于”下，选择要应用此规则的 VM。 使用“添加”按钮将更多 VM 添加到规则。
1. 完成后，单击“创建规则”。
1. 若要删除规则，只需选择它，然后单击“删除规则”即可。

## <a name="using-windows-powershell"></a>使用 Windows PowerShell

与使用 Windows Admin Center 相比，使用 Windows PowerShell 可以创建更复杂的规则。 通常，可以从远程计算机（而不是群集中的主机服务器）设置规则。 此远程计算机称为管理计算机。

从某一管理计算机运行 Windows PowerShell 命令时，请包含使用所管理的群集名称的 `-Name` 或 `-Cluster` 参数。 如果适用，在将 `-ComputerName` 参数用于服务器节点时，还需要指定完全限定的域名 (FQDN)

### <a name="new-powershell-cmdlets"></a>新 PowerShell cmdlet

若要为群集创建相关性规则，请使用下述新的 PowerShell cmdlet：

#### <a name="new-clusteraffinityrule"></a>New-ClusterAffinityRule

`New-ClusterAffinityRule` cmdlet 用于创建新规则。  通过此命令，可指定规则的名称和规则类型，其中：

**`-Name`** 为规则名称

**`-RuleType`** 值为 `SameFaultDomain` | `SameNode` | `DifferentFaultDomain` | `DifferentNode`

示例：

```powershell
New-ClusterAffinityRule -Name -RuleType SameFaultDomain -Cluster Cluster1
```

#### <a name="set-clusteraffinityrule"></a>Set-ClusterAffinityRule

`Set-ClusterAffinityRule` cmdlet 用于启用或禁用规则，其中：

**`-Name`** 为要启用或禁用的规则名称

**`-Enabled`**  |  **`Disabled`** 启用或禁用规则

示例：

```powershell
Set-ClusterAffinityRule -Name -Enabled -Cluster Cluster1
```

#### <a name="get-clusteraffinityrule"></a>Get-ClusterAffinityRule

`Get-ClusterAffinityRule` cmdlet 用于显示指定的规则及其类型。  如果未指定 `-Name`，则将列出所有规则。

示例：

```powershell
Get-ClusterAffinityRule -Name -Cluster Cluster1
```

#### <a name="add-clustergrouptoaffinityrule"></a>Add-ClusterGroupToAffinityRule

`Add-ClusterGroupToAffinityRule` cmdlet 用于将 VM 角色或组名称添加到特定的相关性规则，其中：

`-Groups` 为要添加到规则的组或角色的名称

`-Name` 为要添加的规则名称

示例：

```powershell
Add-ClusterGroupToAffinityRule -Groups -Name -Cluster Cluster1
```

#### <a name="add-clustersharedvolumetoaffinityrule"></a>Add-ClusterSharedVolumeToAffinityRule

通过 `Add-ClusterSharedVolumeToAffinityRule`，你的 VM 可与 VHDX 所在的群集共享卷保存在一起，其中：

**`-ClusterSharedVolumes`** 为希望添加到规则的 CSV 磁盘

`-Name` 为要添加的规则名称

示例：

```powershell
Add-ClusterSharedVolumeToAffinityRule  -ClusterSharedVolumes -Name -Cluster Cluster1
```

#### <a name="remove-clusteraffinityrule"></a>Remove-ClusterAffinityRule

`Remove-ClusterAffinityRule` 删除指定的规则，其中 `-Name` 是规则名称 。

示例：

```powershell
Remove-ClusterAffinityRule -Name -Cluster Cluster1
```

#### <a name="remove-clustergroupfromaffinityrule"></a>Remove-ClusterGroupFromAffinityRule

`Remove-ClusterGroupFromAffinityRule` 将从特定规则中删除 VM 组或角色，但不会禁用或删除该规则，其中：

**`-Name`** 为规则名称

**`-Groups`** 为希望从规则中删除的组或角色

示例：

```powershell
Remove-ClusterGroupFromAffinityRule -Name -Groups -Cluster Cluster1
```

#### <a name="remove-clustersharedvolumefromaffinityrule"></a>Remove-ClusterSharedVolumeFromAffinityRule

`Remove-ClusterSharedVolumeFromAffinityRule` cmdlet 用于从特定规则中删除群集共享卷，但不会禁用或删除该规则，其中：

**`-ClusterSharedVolumes`** 为要从规则中删除的 CSV 磁盘

**`-Name`** 为要添加的规则名称

示例：

```powershell
Remove-ClusterSharedVolumeFromAffinityRule -ClusterSharedVolumes -Name -Cluster Cluster1
```

### <a name="existing-powershell-cmdlets"></a>现有的 PowerShell cmdlet

随着新 cmdlet 的出现，我们还向一些现有的 cmdlet 添加了其他新开关。

#### <a name="move-clustergroup"></a>Move-ClusterGroup

新 `-IgnoreAffinityRule` 开关将忽略规则并将群集资源组移动到另一个群集节点。 有关此 cmdlet 的详细信息，请参阅 [Move-ClusterGroup](https://docs.microsoft.com/powershell/module/failoverclusters/move-clustergroup)。

示例：

```powershell
Move-ClusterGroup -IgnoreAffinityRule -Cluster Cluster1
```

> [!NOTE]
> 如果移动规则有效（受支持），所有受影响的组和角色也将移动。  如果 VM 移动需要故意临时违反一个规则一次，则可使用 `-IgnoreAffinityRule` 开关来允许此移动。 在这种情况下，将显示 VM 的违规警告。 之后，可根据需要重新启用该规则。

#### <a name="start-clustergroup"></a>Start-ClusterGroup

新 `-IgnoreAffinityRule` 开关将忽略规则并使群集资源组在其当前位置处于联机状态。 有关此 cmdlet 的详细信息，请参阅 [Start-ClusterGroup](https://docs.microsoft.com/powershell/module/failoverclusters/start-clustergroup)。

示例：

```powershell
Start-ClusterGroup -IgnoreAffinityRule -Cluster Cluster1
```

## <a name="affinity-rule-examples"></a>相关性规则示例

相关性规则是将资源保存在同一服务器、群集或站点上的“聚合”规则。 下面是设置相关性规则的几个常见场景。

### <a name="scenario-1"></a>方案 1

假设你有一个 SQL Server VM 和一个 Web 服务器 VM。 这两个 VM 需要始终保留在同一站点中，但不必位于站点中的同一群集节点上。  可使用 `SameFaultDomain`，如下所示：

```powershell
New-ClusterAffinityRule -Name WebData -Ruletype SameFaultDomain -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,WEB1 –Name WebData -Cluster Cluster1

Set-ClusterAffinityRule -Name WebData -Enabled 1 -Cluster Cluster1
```

若要查看此规则及其配置方式，请使用 `Get-ClusterAffinityRule` cmdlet 查看输出：

```powershell
Get-ClusterAffinityRule -Name WebData -Cluster Cluster1

Name        RuleType          Groups        Enabled
----        ---------         ------        -------
WebData     SameFaultDomain   {SQL1, WEB1}     1
```

### <a name="scenario-2"></a>方案 2

接下来使用上述同一场景，只是指定 VM 必须位于同一群集节点上，但不必在同一站点中。 可使用 `SameNode` 按如下所示进行设置：

```powershell
New-ClusterAffinityRule -Name WebData1 -Ruletype SameNode -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,WEB1 –Name WebData1 -Cluster Cluster1

Set-ClusterAffinityRule -Name WebData1 -Enabled 1 -Cluster Cluster1
```

若要查看规则及其配置方式，请使用 `Get-ClusterAffinityRule` cmdlet 查看输出：

```powershell
Get-ClusterAffinityRule -Name WebData1 -Cluster Cluster1

Name    RuleType    Groups        Enabled
----    --------    ------        -------
DC      SameNode    {SQL1, WEB1}     1
```

## <a name="anti-affinity-rule-examples"></a>反相关性规则示例

反相关性规则是“分开”规则，用于分离资源并将其放置在不同的服务器、群集或站点上。

### <a name="scenario-1"></a>方案 1
在同一 Azure Stack HCI 多站点群集上有两个 VM 都运行 SQL Server。  每个 VM 都利用大量内存、CPU 和存储资源。  如果这两个 VM 最终位于同一节点上，这可能会导致其中一个或两个都出现性能问题，因为它们会争用内存、CPU 和存储周期。  使用以 `DifferentNode` 为规则类型的反相关性规则时，这些 VM 将始终保留在不同的群集节点上。  

此操作的示例命令如下：

```powershell
New-ClusterAffinityRule -Name SQL -Ruletype DifferentNode -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,SQL2 –Name SQL -Cluster Cluster1

Set-ClusterAffinityRule -Name SQL -Enabled 1 -Cluster Cluster1
```

若要查看规则及其配置方式，请使用 `Get-ClusterAffinityRule` cmdlet 查看输出：

```powershell
Get-ClusterAffinityRule -Name SQL -Cluster Cluster1

Name    RuleType        Groups        Enabled
----    -----------     -------       -------
SQL     DifferentNode   {SQL1, SQL2}     1
```

### <a name="scenario-2"></a>方案 2

假设有一个 Azure Stack HCI 延伸群集，其中有两个站点（容错域）。 你有两个域控制器，希望将它们保留在不同站点中。 使用以 `DifferentFaultDomain` 为规则类型的反相关性规则时，这些域控制器将始终保留在不同站点中。  此操作的示例命令如下：

```powershell
New-ClusterAffinityRule -Name DC -Ruletype DifferentFaultDomain -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups DC1,DC2 –Name DC -Cluster Cluster1

Set-ClusterAffinityRule -Name DC -Enabled 1 -Cluster Cluster1
```

若要查看此规则及其配置方式，请使用 `Get-ClusterAffinityRule` cmdlet 查看输出：

```powershell
Get-ClusterAffinityRule -Name DC -Cluster Cluster1

Name    RuleType                Groups        Enabled
----    --------                -------       -------
DC      DifferentFaultDomain    {DC1, DC2}       1
```

## <a name="combined-rule-examples"></a>组合规则示例

结合使用相关性和反相关性规则，可在多站点群集中轻松配置各种 VM 组合。  在此场景中，每个站点都有三个 VM：SQL Server (SQL)、Web 服务器 (WEB) 和域控制器 (DC)。  对于每个组合，可使用相关性规则和 `SameFaultDomain` 将它们全部放在同一站点中。  还可使用反相关性规则和 `DifferentFaultDomain` 为每个站点设置域控制器，将这些域控制器 VM 保存在不同站点中，如下所示：

```powershell
New-ClusterAffinityRule -Name Site1Trio -Ruletype SameFaultDomain -Cluster Cluster1

New-ClusterAffinityRule -Name Site2Trio -Ruletype SameFaultDomain -Cluster Cluster1

New-ClusterAffinityRule -Name TrioApart -Ruletype DifferentFaultDomain -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,WEB1,DC1 –Name Site1Trio -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL2,WEB2,DC2 –Name Site2Trio -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups DC1,DC2 –Name TrioApart -Cluster Cluster1

Set-ClusterAffinityRule -Name Site1Trio -Enabled 1 -Cluster Cluster1

Set-ClusterAffinityRule -Name Site2Trio -Enabled 1 -Cluster Cluster1

Set-ClusterAffinityRule -Name TrioApart -Enabled 1 -Cluster Cluster1
```

若要查看规则及其配置方式，请使用不带 `-Name` 开关的 `Get-ClusterAffinityRule` cmdlet，你可看到创建的所有规则及其输出。

```powershell
Get-ClusterAffinityRule -Cluster Cluster1

Name        RuleType               Groups            Enabled
----        --------               ------            -------
Site1Trio   SameFaultDomain        {SQL1, WEB1, DC1}    1
Site2Trio   SameFaultDomain        {SQL2, WEB2, DC2}    1
TrioApart   DifferentFaultDomain   {DC1, DC2}           1
```

## <a name="storage-affinity-rules"></a>存储相关性规则

还可在同一群集节点上的群集共享卷 (CSV) 上保留 VM 及其 VHDX。 这可以防止发生 CSV 重定向，这种重定向可能会降低 VM 的启动或停止速度。  考虑到前面合并的相关性和反相关性场景，可将 SQL VM 和群集共享卷保留在同一群集节点上。  为此，请使用以下命令：

```powershell
New-ClusterAffinityRule -Name SQL1CSV1 -Ruletype SameNode -Cluster Cluster1

New-ClusterAffinityRule -Name SQL2CSV2 -Ruletype SameNode -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1 –Name SQL1CSV1 -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL2 –Name SQL2CSV2 -Cluster Cluster1

Add-ClusterSharedVolumeToAffinityRule -ClusterSharedVolumes CSV1 -Name SQL1CSV1 -Cluster Cluster1

Add-ClusterSharedVolumeToAffinityRule -ClusterSharedVolumes CSV2 -Name SQL2CSV2 -Cluster Cluster1

Set-ClusterAffinityRule -Name SQL1CSV1 -Enabled 1 -Cluster Cluster1

Set-ClusterAffinityRule -Name SQL2CSV2 -Enabled 1 -Cluster Cluster1
```

若要查看这些规则以及它们的配置方式，请使用不带 -Name 开关的 `Get-ClusterAffinityRule` cmdlet 并查看输出。

```powershell
Get-ClusterAffinityRule -Cluster Cluster1

Name        RuleType               Groups            Enabled
----        --------               ------            -------
Site1Trio   SameFaultDomain        {SQL1, WEB1, DC1}    1
Site2Trio   SameFaultDomain        {SQL2, WEB2, DC2}    1
TrioApart   DifferentFaultDomain   {DC1, DC2}           1
SQL1CSV1    SameNode               {SQL1, <CSV1-GUID>}  1
SQL2CSV2    SameNode               {SQL2, <CSV2-GUID>}  1
```

## <a name="next-steps"></a>后续步骤

了解如何管理 VM。 请参阅[使用 Windows Admin Center 管理 Azure Stack HCI 上的 VM](../manage/vm.md)。
