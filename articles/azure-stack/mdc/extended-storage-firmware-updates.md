---
title: Azure Stack - 更新 Modular Data Center 扩展存储的固件
description: 本文介绍如何在 Modular Data Center blob 存储的扩展存储中执行固件更新。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 10/14/2020
ms.date: 11/09/2020
ms.author: v-jay
ms.reviewer: karlt
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 9c4b22c0966faa47777dc0fb748bb525b684aeb6
ms.sourcegitcommit: f187b1a355e2efafea30bca70afce49a2460d0c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330733"
---
# <a name="updating-onefs-and-firmware-for-the-datacenter-extended-storage"></a>更新数据中心扩展存储的 OneFS 和固件

本文介绍如何更新数据中心扩展存储硬件的 OneFS 和固件。 

扩展存储硬件集成和部署中的最后一个步骤是验证硬件是否正常以及是否可以进行配置。 [检查扩展存储的运行状况](extended-storage-health-checks.md)中介绍了这些步骤。

## <a name="update-onefs"></a>更新 OneFS

在更新固件之前更新 OneFS，以便应用最新的操作系统补丁。 

1. 验证群集是否有足够的可用空间来安装此补丁。
1. 在群集中的任何节点上打开一个 SSH 连接，使用 root 帐户登录。

   >[!NOTE]
   >如果在合规性模式群集上安装补丁，请使用 compadmin 帐户登录。

1. 将 8.2.2_GA-RUP_2020-07_PSP-40.tgz 文件复制到群集上的 /ifs/data/Isilon_Support 目录。
1. 运行以下命令以切换到 /ifs/data/Isilon_Support 目录： 

   ```
   /ifs/data/Isilon_Support
   pwd
   ```

1. 若要解压缩补丁文件，请运行以下命令：

   ```
   -zxvf 8.2.2_GA-RUP_2020-07_PSP-40.tgz
   ```

1. 若要安装此补丁，请运行以下命令：
   >[!WARNING]
   >以下命令将同时重启群集中的所有节点。 若要安装补丁，然后连续重启每个节点，请省略 `-simultaneous` 参数。 有关更多信息，请参阅自述文件中的“安装/删除影响“部分。

   ```
   isi upgrade patches install 8.2.2_GA-RUP_2020-07_PSP-40.pkg --simultaneous
   ```

1. 若要验证是否已安装此补丁，请运行以下命令：

   ```
   isi upgrade patches list
   ```

1. 确认 8.2.2_GA RUP_2020 07_PSP-40 显示在已安装包的列表中，并且状态设置为“已安装”。

## <a name="update-firmware-on-nodes"></a>更新节点上的固件

   1. 查看当前的驱动器固件和驱动器支持[包发布](https://aka.ms/currentextstoragerelease)。
   1. 将文件下载到本地计算机。  
   1. 将驱动器和节点固件包上传到 Isilon 文件共享，并将文件放在以下相应目录中：
    
        * Drive_Support_*.tgz：/ifs/data/Isilon_Support/dsp/
        * IsiFw_Package_*.tar：/ifs/data 

## <a name="verify-disk-drive-firmware"></a>验证磁盘驱动器固件

首先执行驱动器支持更新/验证。 这将检查磁盘驱动器固件是否与提供的包一样保持最新。 如果不是，则可以使用包来执行所需的更新：

   1. 从一个活动的 ssh 会话中，运行：

       ```
       cd /ifs/data/Isilon_Support/dsp/
       ```

   1. 通过运行 ls 命令确保 Drive_Support_*.tgz 在此目录中。
   1. 通过运行以下命令展开 .tgz 文件。 请确保你有完整的文件名，并将 * 替换为包的实际版本号：
       ```
       tar -zxvf Drive_Support_v1.*.tgz
       ```
       例如，如果包版本为 Drive_Support_v1.33.tgz，请运行以下命令：
       ```
       tar -zxvf Drive_Support_v1.33.tgz
       ```
   1. 这会展开为三个文件：
    
        * Drive_Support_v1.33.tar
        * Drive_Support_v1.33.tar.md5
        * Drive_Support_v1.33.tar.sha256
   1. 运行以下命令以安装/验证驱动器支持软件：
       ```
       isi_dsp_install Drive_Support_v1.*.tar
       ```
       例如，如果包版本为 Drive_Support_v1.33.tgz，请运行以下命令：
       ```
       isi_dsp_install Drive_Support_v1.33.tar
       ```
   1. 此命令运行检查并请求安装驱动器支持包。 如果该包是必需的，则会安装该包；否则，它会指出它已安装，并跳过对驱动器支持软件的任何进一步更新。 V 1.33 的示例输出如下所示：
    
        ```
        Running local CHECKS for patch Drive_Support_v1.33
        INSTALL operation has been requested for patch Drive_Support_v1.33
        Installed Drive_Support_v1.33
        DELETE operation has been requested for patch
        Drive_Support_v1.33
        Removed Drive_Support_v1.33 package files
        DSP Install Succeeded, 170.605088949 sec elapsed
        ```
        
   1. 可以通过运行以下命令来进行最终确认。 “Desired”列中除“-”以外的任何内容都表示驱动器需要更新：
       ```
       isi devices drive firmware list --node-lnn all
       ```
   1. 如果需要更新某些驱动器，则应使用以下命令来执行磁盘固件更新：
       ```
       isi devices drive firmware update start all --node-lnn all
       ```
   1. 可以使用以下命令监视此过程：
       ```
       isi devices drive firmware update list
       ```
   1. 完成后，可以使用以下命令来验证驱动器的运行状况：
       ```
       isi devices drive list --node-lnn all
       ```
## <a name="install-node-firmware"></a>安装节点固件

完成/验证驱动器支持更新后，可以利用 IsiFw_Package_*.tar 安装节点固件。

   1. 从一个活动的 ssh 会话中，运行：
        ```
        cd /ifs/data/
        ```
   1. 通过运行 ls 命令确保 IsiFw_Package_*.tar 在此目录中。
   1. 运行以下命令，在各个节点上暂存和安装固件。 请确保你有完整的文件名，并将 * 替换为包的实际版本号：
       ```
       isi upgrade patches install IsiFw_Package_v*.tar --rolling=false
       ```
       例如，如果版本号为 v10.3.3.tar，请运行以下命令：
       ```
       isi upgrade patches install IsiFw_Package_v10.3.3.tar --rolling=false
       ```
   1. 执行后，系统会发送以下消息进行确认：
       ```
        Requested install of patch IsiFw_Package_v10.3.3.
       ```
   1. 用以下命令验证阶段/安装的状态：
       ```
       isi upgrade patches list
       ```
   1. 在过程完成后，“Status”列会更新，显示的状态将是：Pending、Unknown、Installing 和 Installed，如以下输出示例所示：
        
       ![固件更新状态](media/extended-storage-firmware-updates/firmware-update-status.png)
        
   1. 现在，IsiFw_Package*.tar 已暂存/安装，接下来可以通过以下命令在各个节点上启动更新的固件的部署：
        ```
        isi upgrade cluster firmware start --no-verify
        ```
   1. 系统会提示你进行以下确认，请键入“yes”以继续：
       ```
       You are about to start a Rolling Firmware UPGRADE, are you sure? (yes/[no]):
       ```
        
       > [!NOTE]
       > 节点固件更新可能需要几个小时，在该过程中节点会脱机。 从节点 1 执行所有这些命令时，连接会断开，因为这是要更新的第一个节点。 OneFS 门户也是如此 – 请等待 5-10 分钟，然后再尝试重新连接以检查状态（如果需要，还可以通过另一个尚未更新的管理界面进行连接）。
   1. 通过活动的管理界面/连接，可以使用以下命令跟踪此固件更新的当前状态：
       ```
       isi upgrade cluster nodes firmware progress list
       ```
       也可以使用 OneFS 门户在“群集管理 - 补丁和固件”下进行跟踪：
        
       ![群集管理](media/extended-storage-firmware-updates/cluster-management.png)
        
       完成后，状态将从“Unknown”变为“Installed”。

## <a name="remove-firmware-packages"></a>删除固件包

在驱动器和节点上完全安装固件包后，可以使用以下命令将其删除。 请确保你有完整的文件名，并将 v* 替换为实际的包版本：
   ```
   isi upgrade patches uninstall IsiFw_Package_ v*.tar --rolling=false
   ```
   例如，如果包版本为 IsiFw_Package_ v10.3.1.tar，请运行以下命令：
   ```
   isi upgrade patches uninstall IsiFw_Package_ v10.3.1.tar --rolling=false
   ```
从一个活动的 ssh 会话中，运行：
   ```
   cd /ifs/data/
   rm -f Drive_Support_v*.tgz
   rm -f Drive_Support_v*.tar
   rm -f Drive_Support_v*.tar.md5
   rm -f Drive_Support_v*.tar.sha256
   ```
   例如：
   ```
   cd /ifs/data/
   rm -f Drive_Support_v10.3.1.tgz
   rm -f Drive_Support_v10.3.1.tar
   rm -f Drive_Support_v10.3.1.tar.md5
   rm -f Drive_Support_v10.3.1.tar.sha256
   ```


        
<!-- ## Contact Dell EMC for support -->

请联系 Azure 支持部门，获取有关任何问题的帮助。

## <a name="next-steps"></a>后续步骤

- [扩展存储运行状况检查](extended-storage-health-checks.md)
