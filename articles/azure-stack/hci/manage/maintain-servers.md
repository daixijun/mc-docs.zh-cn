---
title: 使 Azure Stack HCI 服务器脱机以进行维护
description: 本主题提供有关如何使用 Windows Admin Center 和 PowerShell 正确暂停、排出和恢复运行 Azure Stack HCI 操作系统的服务器的指导。
author: WenJason
ms.author: v-jay
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
origin.date: 10/23/2020
ms.date: 11/09/2020
ms.openlocfilehash: 43f5aa01dde9b5c3d760b7adc562c4ec7b4a786c
ms.sourcegitcommit: f187b1a355e2efafea30bca70afce49a2460d0c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330717"
---
# <a name="taking-an-azure-stack-hci-server-offline-for-maintenance"></a>使 Azure Stack HCI 服务器脱机以进行维护

> 适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

借助 Azure Stack HCI，使服务器脱机以进行维护需要使在群集中所有服务器之间共享的部分存储脱机。 这需要暂停要脱机的服务器、将角色和虚拟机 (VM) 迁移到群集中的其他服务器，并验证所有数据在群集中的其他服务器上可用。 此过程可确保数据在维护期间处于安全和可访问状态。

可以使用 Windows Admin Center 或 PowerShell 使服务器脱机以进行维护。 本主题将对这两种方法进行介绍。

   > [!IMPORTANT]
   > 本主题假设你需要关闭物理服务器的电源以执行维护，或出于其他原因重启物理服务器。 若要安装 Azure Stack HCI 群集的更新，请参阅[更新 Azure Stack HCI 群集](update-cluster.md)，其中介绍了如何使用群集感知更新 (CAU) 自动执行本主题中的所有步骤，同时更新服务器并在必要时重启服务器。

## <a name="take-a-server-offline-using-windows-admin-center"></a>使用 Windows Admin Center 使服务器脱机

准备使 Azure Stack HCI 群集中的服务器脱机的最简单方法是使用 Windows Admin Center。

### <a name="verify-its-safe-to-take-the-server-offline"></a>验证使服务器脱机是安全的

1. 使用 Windows Admin Center，连接到要脱机的服务器。 从“工具”菜单中选择“存储”>“磁盘”，并验证每个虚拟磁盘的“状态”列显示“联机”   。

2. 然后，选择“存储”>“卷”，并验证每个卷的“运行状况”列显示“正常”，且每个卷的“状态”列显示“正常”    。

### <a name="pause-and-drain-the-server"></a>暂停和排出服务器

在关闭或重启服务器之前，应暂停服务器并排出（移出）其上运行的任何群集角色（例如 VM），以确保服务器关闭不会影响应用程序状态。 在使群集服务器脱机以进行维护之前，始终暂停和排出群集服务器。

1. 使用 Windows Admin Center，连接到群集，然后从群集管理器的“工具”菜单中选择“计算”>“节点” 。

2. 单击要暂停和排出的服务器的名称，然后选择“暂停”。 你应看到以下提示：

   如果暂停此节点，所有群集角色都将迁移到其他节点，并且在此节点恢复之前，无法向其添加任何角色。是否确实要暂停群集节点?

3. 选择“是”以暂停服务器并启动排出过程。 群集节点状态将显示为“正在排出”，并且 Hyper-V 和 VM 等角色将立即开始实时迁移到群集中的其他服务器。 这会需要几分钟时间。

   > [!NOTE]
   > 在正确暂停和排出服务器时，Azure Stack HCI 会执行自动安全检查，以确保可以安全地继续操作。 如果有不正常的卷，安全检查会停止，并提醒你继续操作不安全。

### <a name="shut-down-the-server"></a>关闭服务器

服务器在完成排出后，其在 Windows Admin Center 中的状态将显示为“已暂停”。 现在即可安全关闭服务器以便进行维护或重启服务器。

### <a name="resume-the-server"></a>恢复服务器

在准备好让服务器开始重新托管群集角色和 VM 时，只需打开服务器，等待它启动，然后使用以下步骤恢复服务器即可。

1. 在群集管理器中，从左侧的“工具”菜单中选择“计算”>“节点” 。

2. 选择要恢复的服务器的名称，然后单击“恢复”。 你应看到以下提示：

   是否确实要恢复群集节点?

3. 在大多数情况下，应选中“将群集角色传输回此节点”复选框。 选择“是”以恢复服务器。

如果选中上述步骤 3 中的框，群集角色和 VM 将立即开始实时迁移回服务器。 这会需要几分钟时间。

### <a name="wait-for-storage-to-resync"></a>等待存储重新同步

当服务器恢复时，在服务器不可用时发生的任何新写入都需要重新同步。 该过程使用智能更改跟踪自动进行。 无需扫描或同步所有数据；仅扫描或同步更改的数据即可。 此过程会受到限制，以缓解生产负载产生的影响。 根据服务器暂停的时间以及写入的新数据量，此过程可能需要很长时间才能完成。

   > [!IMPORTANT]
   > 必须先等待重新同步完成，然后才能使群集中的任何其他服务器脱机。

若要检查重新同步是否已完成，请使用 Windows Admin Center 连接到服务器，并从左侧的“工具”菜单中选择“存储”>“卷”，然后选择页面顶部附近的“卷”  。 如果每个卷的“运行状况”列显示“正常”，并且每个卷的“状态”列显示“正常”，则重新同步已完成，现在可以安全地使群集中的其他服务器脱机   。

## <a name="take-a-server-offline-using-powershell"></a>使用 PowerShell 使服务器脱机

使用以下过程，通过 PowerShell 正确暂停、排出和恢复 Azure Stack HCI 群集中的服务器。

### <a name="verify-its-safe-to-take-the-server-offline"></a>验证使服务器脱机是安全的

若要验证所有卷都运行正常，请以管理员身份运行以下 cmdlet：

```PowerShell
Get-VirtualDisk
```

下面是此输出的执行示例：

```
FriendlyName              ResiliencySettingName FaultDomainRedundancy OperationalStatus HealthStatus    Size FootprintOnPool StorageEfficiency
------------              --------------------- --------------------- ----------------- ------------    ---- --------------- -----------------
Mirror II                 Mirror                1                     OK                Healthy         4 TB         8.01 TB            49.99%
Mirror-accelerated parity                                             OK                Healthy      1002 GB         1.96 TB            49.98%
Mirror                    Mirror                1                     OK                Healthy         1 TB            2 TB            49.98%
ClusterPerformanceHistory Mirror                1                     OK                Healthy        24 GB           49 GB            48.98%
```

验证每个卷的 HealthStatus 属性为“正常”，且 OperationalStatus 显示“正常”  。

### <a name="pause-and-drain-the-server"></a>暂停和排出服务器

以管理员身份运行以下 cmdlet，以暂停和排出服务器：

```PowerShell
Suspend-ClusterNode -Drain
```

### <a name="shut-down-the-server"></a>关闭服务器

服务器在完成排出后，其在 PowerShell 中将显示为“已暂停”。

现在可以使用 `Stop-Computer` 或 `Restart-Computer` PowerShell cmdlet 安全关闭服务器或重启服务器。

   > [!NOTE]
   > 在正在关闭或启动/停止群集服务的服务器上运行 `Get-VirtualDisk` 命令时，服务器的“操作状态”可能报告为“未完成”或“已降级”，并且“运行状况状态”列可能会列出警告。 这是正常情况，不会导致问题。 你的所有卷都保持联机和可访问。

### <a name="resume-the-server"></a>恢复服务器

以管理员身份运行以下 cmdlet，以将服务器恢复到群集。 若要返回之前在服务器上运行的群集角色和 VM，请使用可选的 -Failback 标志：

```PowerShell
Resume-ClusterNode –Failback Immediate
```

服务器在恢复后，其在 PowerShell 中将显示为“启动”。

### <a name="wait-for-storage-to-resync"></a>等待存储重新同步

当服务器恢复后，必须先等待重新同步完成，然后才能使群集中的任何其他服务器脱机。

以管理员身份运行以下 cmdlet 以监视进度：

```PowerShell
Get-StorageJob
```

如果重新同步已完成，你将不会收到任何输出。

下面的一些示例输出显示仍在运行的重新同步（修复）作业：

```
Name   IsBackgroundTask ElapsedTime JobState  PercentComplete BytesProcessed BytesTotal
----   ---------------- ----------- --------  --------------- -------------- ----------
Repair True             00:06:23    Running   65              11477975040    17448304640
Repair True             00:06:40    Running   66              15987900416    23890755584
Repair True             00:06:52    Running   68              20104802841    22104819713
```

BytesTotal 列显示需要重新同步的存储量。 PercentComplete 列显示进度。

   > [!WARNING]
   > 在修复作业完成之前，使其他服务器脱机都是不安全的。

在此期间，在 HealthStatus 下，卷将继续显示为“警告”，这很正常 。

例如，如果在存储正在重新同步时使用 `Get-VirtualDisk` cmdlet，可能会看到以下输出：

```
FriendlyName ResiliencySettingName OperationalStatus HealthStatus IsManualAttach Size
------------ --------------------- ----------------- ------------ -------------- ----
MyVolume1    Mirror                InService         Warning      True           1 TB
MyVolume2    Mirror                InService         Warning      True           1 TB
MyVolume3    Mirror                InService         Warning      True           1 TB
```

作业完成后，使用 `Get-VirtualDisk` cmdlet 再次验证卷是否显示 **正常**。 下面是一些示例输出：

```
FriendlyName ResiliencySettingName OperationalStatus HealthStatus IsManualAttach Size
------------ --------------------- ----------------- ------------ -------------- ----
MyVolume1    Mirror                OK                Healthy      True           1 TB
MyVolume2    Mirror                OK                Healthy      True           1 TB
MyVolume3    Mirror                OK                Healthy      True           1 TB
```

现在可以安全地暂停和重启群集中的其他服务器。

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [存储空间直通概述](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [更新 Azure Stack HCI 群集](update-cluster.md)