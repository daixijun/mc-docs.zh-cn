---
title: Azure 虚拟机上 SQL Server 的文档更改
description: 了解 Azure 虚拟机上 SQL Server 的各个版本的新功能和改进。
services: virtual-machines-windows
author: WenJason
ms.author: v-jay
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: reference
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
origin.date: 10/15/2020
ms.date: 01/04/2021
ms.openlocfilehash: 604beec11853f3e4c786f6da2bf3d76c91c291ae
ms.sourcegitcommit: cf3d8d87096ae96388fe273551216b1cb7bf92c0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97830387"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Azure 虚拟机上 SQL Server 的文档更改
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure 允许你使用内置的 SQL Server 映像部署虚拟机 (VM)。 本文汇总了 Azure 虚拟机上的 SQL Server 的最新版本中与新功能和改进相关的文档更改。 

## <a name="october-2020"></a>2020 年 10 月

| 更改 | 详细信息 |
| --- | --- |
| AG 的 DNN | 你现在可以为 SQL Server 2019 CU8 及更高版本配置[分布式网络名称 (DNN) 侦听器](availability-group-distributed-network-name-dnn-listener-configure.md)来替换传统的 [VNN 侦听器](availability-group-overview.md#connectivity)，这样就不再需要使用 Azure 负载均衡器。   | 


## <a name="june-2020"></a>2020 年 6 月

| 更改 | 详细信息 |
| --- | --- |
| **分布式网络名称 (DNN)** | Windows Server 2016+ 上的 SQL Server 2019 现在以预览版形式支持使用[分布式网络名称](./failover-cluster-instance-distributed-network-name-dnn-configure.md)而非 Azure 负载均衡器将流量路由到故障转移群集实例 (FCI)。 此支持简化了连接到 Azure 中的高可用性 (HA) 解决方案的操作。 | 
| 使用 Azure 共享磁盘的 FCI | 现在可以使用 [Azure 共享磁盘](failover-cluster-instance-azure-shared-disks-manually-configure.md)部署[故障转移群集实例 (FCI)](failover-cluster-instance-overview.md)。 |
| 重新组织的 FCI 文档 | 为了清晰明了，有关 [Azure VM 上 SQL Server 的故障转移群集实例](failover-cluster-instance-overview.md)的文档已重新编写并重新组织。 我们已拆分了一些配置内容，例如[群集配置最佳做法](hadr-cluster-best-practices.md)以及 [Azure 负载均衡器](./availability-group-vnn-azure-load-balancer-configure.md)配置方法。 | 
| &nbsp; | &nbsp; |


## <a name="may-2020"></a>2020 年 5 月 

| 更改 | 详细信息 |
| --- | --- |
| Azure SQL 系列 | Azure 虚拟机上的 SQL Server 现在是 [Azure SQL 产品系列](../../azure-sql-iaas-vs-paas-what-is-overview.md)的一部分。 查看我们的[新外观](../index.yml)！ 产品未发生任何更改，更改文档是为了使 Azure SQL 产品决策更为容易。 | 


## <a name="2019"></a>2019

|更改 | 详细信息 |
 --- | --- |
|性能优化的存储配置 | 现在，在创建新的 SQL Server VM 时，可实现[完全自定义存储配置](storage-configuration.md#new-vms)。 |
|**适用于 FCI 的高级文件共享** | 现在可使用[高级文件共享](failover-cluster-instance-premium-file-share-manually-configure.md)而非[存储空间直通](failover-cluster-instance-storage-spaces-direct-manually-configure.md)原始方法来创建故障转移群集实例。 
| **Azure 专用主机** | 可在 [Azure 专用主机](dedicated-host.md)上运行 SQL Server VM。 | 
| 将 SQL Server VM 迁移到其他区域 | 使用 Azure Site Recovery [将 SQL Server VM 从一个区域迁移到另一个区域](move-sql-vm-different-region.md)。 |
| 扩展了对 SQL Server 2008 和 2008 R2 的支持 | 通过“按原样”迁移到 Azure VM，[扩展对 SQL Server 2008 和 SQL Server 2008 R2 的支持](sql-server-2008-extend-end-of-support.md)。 | 
| **门户增强功能** | 改进了 Azure 门户的 SQL Server VM 部署体验，从而提高了可用性。 有关详细信息，请参见简要的[快速入门](sql-vm-create-portal-quickstart.md)和更详尽的[操作指南](create-sql-vm-portal.md)，以部署 SQL Server VM。|
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>其他资源

**Windows VM**：

* [Windows VM 上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [在 Windows VM 上预配 SQL Server](create-sql-vm-portal.md)
* [将数据库迁移到 Azure VM 上的 SQL Server](migrate-to-vm-from-sql-server.md)
* [Azure 虚拟机中 SQL Server 的高可用性和灾难恢复](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Azure 虚拟机中 SQL Server 的性能最佳做法](performance-guidelines-best-practices.md)
* [Azure 虚拟机中的 SQL Server 的应用程序模式和开发策略](application-patterns-development-strategies.md)

**Linux VM**：

* [Linux VM 上的 SQL Server 概述](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [在 Linux 虚拟机上预配 SQL Server](../linux/sql-vm-create-portal-quickstart.md)
* [常见问题 (Linux)](../linux/frequently-asked-questions-faq.md)
* [“Linux 上的 SQL Server”文档](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)