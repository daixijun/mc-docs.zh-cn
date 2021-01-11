---
title: 在 Azure 门户中对 Windows VM 进行故障排除 | Azure
description: 了解如何通过 Azure 门户将 OS 磁盘连接到恢复 VM，以便排查 Azure 中的 Windows 虚拟机问题。
services: virtual-machines-windows
manager: dcscontentpm
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
origin.date: 08/19/2018
author: rockboyfor
ms.date: 01/04/2021
ms.testscope: yes
ms.testdate: 08/31/2020
ms.author: v-yeche
ms.openlocfilehash: f10ef39d2c357ea9e3234711660e267c280354c1
ms.sourcegitcommit: b4fd26098461cb779b973c7592f951aad77351f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857142"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-through-the-azure-portal"></a>通过 Azure 门户将 OS 磁盘附加到恢复 VM，对 Windows VM 进行故障排除
如果 Azure 中的 Windows 虚拟机 (VM) 遇到启动错误或磁盘错误，可能需要对虚拟硬盘 (VHD) 执行故障排除步骤。 一个常见示例是应用程序更新失败，导致 VM 无法成功启动。 本文详细介绍如何使用 Azure 门户将虚拟硬盘连接到另一个 Windows VM 来修复所有错误，然后重新创建原始 VM。 

## <a name="recovery-process-overview"></a>恢复过程概述
故障排除过程如下：

1. 停止受影响的 VM。
1. 为 VM 的 OS 磁盘创建快照。
1. 从快照创建虚拟硬盘。
1. 将虚拟硬盘附加并装入到另一个 Windows VM，以便进行故障排除。
1. 连接到故障排除 VM。 编辑文件或运行任何工具来修复原始虚拟硬盘上的问题。
1. 从故障排除 VM 卸载并分离虚拟硬盘。
1. 交换 VM 的 OS 磁盘。

> [!NOTE]
> 本文不适用于包含非托管磁盘的 VM。

## <a name="take-a-snapshot-of-the-os-disk"></a>拍摄 OS 磁盘快照
快照是虚拟硬盘的完整只读副本。 建议在创建快照之前完全关闭 VM，以清除正在运行的所有进程。 若要创建 OS 磁盘的快照，请执行以下步骤：

1. 转到 [Azure 门户](https://portal.azure.cn)。 在边栏中选择“虚拟机”，然后选择有问题的 VM。
1. 在左窗格中选择“磁盘”，然后选择 OS 磁盘的名称。 
    
    :::image type="content" source="./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png" alt-text="屏幕截图显示了磁盘设置中的 OS 磁盘的名称。":::
1. 在 OS 磁盘的“概述”页上，选择“创建快照”。 
1. 在 OS 磁盘所在位置创建快照。

## <a name="create-a-disk-from-the-snapshot"></a>从快照创建磁盘
若要从快照创建磁盘，请执行以下步骤：

<!--Not Available on **Cloud Shell**-->
<!--Not Available on ![Image about Open local Shell](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)-->

1. 运行以下 PowerShell 命令从快照创建托管磁盘。 将示例名称替换为相应的名称。

    ```powershell
    # Sign in the Azure China Cloud
    Connect-AzAccount -Environment AzureChinaCloud
    
    #Provide the name of your resource group.
    $resourceGroupName ='myResourceGroup'

    #Provide the name of the snapshot that will be used to create managed disks.
    $snapshotName = 'mySnapshot' 

    #Provide the name of the managed disk.
    $diskName = 'newOSDisk'

    #Provide the size of the disks in gigabytes. It should be greater than the VHD file size. In this example, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'

    #Provide the storage type for the managed disk: Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'

    #Provide the Azure region (for example, chinanorth) where the managed disks will be located.
    #This location should be the same as the snapshot location.
    #Get all the Azure locations by using this command:
    #Get-AzLocation
    $location = 'chinanorth'

    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 

    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id

    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. 如果命令运行成功，则会在提供的资源组中看到新磁盘。

## <a name="attach-the-disk-to-another-vm"></a>将磁盘附加到另一个 VM
在后续几个步骤中，将使用另一个 VM 进行故障排除。 将磁盘附加到故障排除 VM 后，可以浏览和编辑磁盘的内容。 此过程允许用户更正任何配置错误或者查看其他应用程序或系统日志文件。 若要将磁盘附加到另一个 VM，请执行以下步骤：

1. 在门户中选择资源组，然后选择故障排除 VM。 选择“磁盘” > “编辑” > “添加数据磁盘”。  

    :::image type="content" source="./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png" alt-text="屏幕截图显示了门户中用于附加现有磁盘的选择。":::

2. 在“数据磁盘”  列表中，选择所标识的 VM 的 OS 磁盘。 如果看不到 OS 磁盘，请确保故障排除 VM 和 OS 磁盘位于同一区域（位置）。 

3. 选择“保存”应用所做的更改。 

## <a name="mount-the-attached-data-disk-to-the-vm"></a>将附加的数据磁盘装载到 VM

1. 与故障排除 VM 建立远程桌面连接。 

2. 在故障排除 VM 中打开“服务器管理器”，然后选择“文件和存储服务”。  

    :::image type="content" source="./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png" alt-text="屏幕截图显示了如何在“服务器管理器”中选择“文件和存储服务”。":::

3. 系统会自动检测并附加数据磁盘。 若要查看已连接磁盘的列表，请选择“磁盘”  。 可选择要查看卷信息（包括驱动器号）的数据磁盘。 以下示例显示使用了驱动器 F 的附加数据磁盘。

    :::image type="content" source="./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png" alt-text="屏幕截图显示了服务器管理器中的附加磁盘和卷信息。":::

## <a name="fix-problems-on-the-original-virtual-hard-disk"></a>修复原始虚拟硬盘上的问题
装载现有虚拟硬盘后，可以根据需要执行任何维护和故障排除步骤。 解决所有错误后，请继续执行以下步骤。

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>卸载并分离原始虚拟硬盘
从故障排除 VM 中分离现有虚拟硬盘。 在解除将虚拟硬盘附加到故障排除 VM 的租约之前，不能将该虚拟硬盘与任何其他 VM 一起使用。

1. 在与 VM 的远程桌面会话中，打开“服务器管理器”，然后选择“文件和存储服务”。

    :::image type="content" source="./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png" alt-text="屏幕截图显示了在“服务器管理器”中选择“文件和存储服务”。":::

2. 选择“磁盘”，右键单击你的数据磁盘，然后选择“脱机”。 

    :::image type="content" source="./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png" alt-text="屏幕截图显示了在“服务器管理器”中将数据磁盘设置为脱机。":::

3. 从 VM 中分离虚拟硬盘。 在 Azure 门户中选择你的 VM，然后选择“磁盘”。 
4. 依次选择“编辑”、附加的 OS 磁盘、“删除” 。

    :::image type="content" source="./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png" alt-text="屏幕截图显示了用于分离现有虚拟硬盘的选择。":::

    继续操作之前，请等待数据磁盘在 VM 中成功删除。

## <a name="swap-the-os-disk-for-the-vm"></a>交换 VM 的 OS 磁盘

Azure 门户现在支持更改 VM 的 OS 磁盘。 为此，请按照下列步骤进行操作：

1. 转到 [Azure 门户](https://portal.azure.cn)。 在边栏中选择“虚拟机”，然后选择有问题的 VM。
1. 在左窗格中选择“磁盘”，然后选择“交换 OS 磁盘”。 

    :::image type="content" source="./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png" alt-text="屏幕截图显示了 Azure 门户中用于交换 OS 磁盘的按钮。":::

1. 选择已修复的新磁盘，然后输入 VM 的名称以确认更改。 如果在列表中看不到该磁盘，请在从故障排除 VM 中分离磁盘后等待 10 到 15 分钟。 另外，请确保该磁盘与 VM 位于同一位置。
1. 选择“确定”  。

## <a name="next-steps"></a>后续步骤
如果在连接到 VM 时遇到问题，请参阅[排查 Azure 虚拟机的远程桌面连接问题](troubleshoot-rdp-connection.md)。 如果在访问 VM 上运行的应用程序时遇到问题，请参阅[排查 Windows VM 上的应用程序连接问题](troubleshoot-app-connection.md)。

有关使用 Azure 资源管理器的详细信息，请参阅 [Azure 资源管理器概述](../../azure-resource-manager/management/overview.md)。

<!-- Update_Description: update meta properties, wording update, update link -->