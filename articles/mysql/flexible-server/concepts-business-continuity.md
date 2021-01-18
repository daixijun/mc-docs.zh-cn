---
title: 业务连续性 - Azure Database for MySQL 灵活服务器概述
description: 了解有关使用 Azure Database for MySQL 灵活服务器实现业务连续性的概念
author: WenJason
ms.author: v-jay
ms.service: mysql
ms.topic: conceptual
origin.date: 09/21/2020
ms.date: 01/11/2021
ms.openlocfilehash: ab9c8bc227880a8827afcdb683310bbba15fa6f8
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98023397"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mysql---flexible-server-preview"></a>有关使用 Azure Database for MySQL - 灵活服务器（预览版）实现业务连续性的概述

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

Azure Database for MySQL 灵活服务器支持业务连续性功能，可在发生计划内和计划外中断时保护数据库。 自动备份和高可用性等功能可实现不同级别的故障保护，具有不同的恢复时间和数据丢失情况。 在构建应用程序以防止故障时，应考虑每个应用程序的恢复时间目标 (RTO) 和恢复点目标 (RPO)。 RTO 是指故障时间容错，RPO 是指数据库服务中断后的数据丢失容错。

下表说明了灵活服务器提供的功能。

| **功能** | **说明** | **限制** |
| ---------- | ----------- | ------------ |
| **备份和恢复** | 灵活服务器自动执行数据库文件的每日备份，并连续备份事务日志。 备份可以保留 1 至 35 天。 可将数据库服务器还原到备份保持期内的任何时间点。 恢复时间将取决于要还原的数据的大小 + 执行日志恢复的时间。 请参阅[概念 - 备份和还原](./concepts-backup-restore.md)，了解更多详细信息。 |备份数据保留在该区域中 |
| **本地冗余备份** | 灵活服务器备份自动并安全地存储在某一区域的本地冗余存储中。 本地冗余备份在主要区域中的单个物理位置内复制服务器备份数据文件三次。 本地冗余备份存储可在一年中提供至少 99.999999999%（11 个 9）的对象持久性。 请参阅[概念 - 备份和还原](./concepts-backup-restore.md)，了解更多详细信息。| 适用于所有区域 |
| **高级文件共享** | 数据库文件存储在高度持久且可靠的 Azure 高级文件共享中，这些文件共享提供了数据冗余，将三个副本存储一个区域中，并且具有数据自动恢复功能。 请参阅[高级文件共享](../../storage/files/storage-how-to-create-premium-fileshare.md)，了解更多详细信息。 | 存储在区域中的数据 |

> [!IMPORTANT]
> 预览期间不提供运行时间、RTO 和 RPO SLA。 本页提供的详细信息仅供参考和计划使用。

## <a name="planned-downtime-mitigation"></a>缓解计划内停机

下面是一些产生故障时间的计划内维护场景：

| **方案** | **处理**|
| :------------ | :----------- |
| **计算缩放（用户）**| 在执行计算缩放操作时，将使用缩放的计算配置来预配新的灵活服务器。 在现有的数据库服务器中，将允许处于活动状态的检查点完成，客户端连接将排空，所有未提交的事务将取消，然后将关闭该服务器。 然后，存储会连接到新服务器，而数据库也会启动，该数据库在接受客户端连接前会根据需要执行恢复。 |
| **新软件部署 (Azure)** | 新功能的推出或 bug 修复作为服务的计划内维护的一部分自动发生，你可以安排这些活动发生的时间。 有关详细信息，请参阅[文档](https://portal.azure.cn/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/plannedMaintenance)并检查你的[门户](https://portal.azure.cn/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/plannedMaintenance) |
| **次要版本升级 (Azure)** | Azure Database for MySQL 会自动将数据库服务器修补到 Azure 确定的次要版本。 这是在服务的计划内维护过程中发生的。 这会导致短暂的停机（以秒为单位），并且会自动重启装有新次要版本的数据库服务器。 有关详细信息，请参阅[文档](../concepts-monitoring.md#planned-maintenance-notification)并检查你的[门户](https://portal.azure.cn/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/plannedMaintenance)。|

## <a name="unplanned-downtime-mitigation"></a>缓解计划外停机

意外的故障（包括基础硬件故障、网络问题和软件 bug）可能会产生计划外故障时间。 如果数据库服务器意外关闭（且已配置高可用性 [HA]），则会激活备用副本。 如果没有，则会自动预配新的数据库服务器。 虽然计划外故障无法避免，但灵活服务器可以通过在数据库服务器和存储层上自动执行恢复操作来减少故障时间，无需人工干预。

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>计划外停机：故障场景和服务恢复

下面是一些计划外故障场景和恢复过程：

| **方案** | **恢复过程 [非 HA]** | **恢复过程 [HA]** |
| :---------- | ---------- | ------- |
| **数据库服务器故障** | 如果数据库服务器由于某些基础硬件故障而关闭，则会丢弃处于活动状态的连接，并中止任何正在进行的事务。 Azure 将尝试重启数据库服务器。 如果成功，则会执行数据库恢复。 如果重启失败，则数据库服务器将尝试在另一物理节点上重启。  <br /> <br /> 恢复时间 (RTO) 取决于各种因素，包括发生故障时的活动，例如，在数据库服务器启动过程中需执行的大型事务和恢复量。 <br /> <br /> 所构建的使用 MySQL 数据库的应用程序需要能够检测并重试丢弃的连接和失败的事务。  当应用程序重试时，连接将定向到新创建的数据库服务器。 | 如果检测到数据库服务器故障，则会激活备用数据库服务器，从而减少故障时间。 RTO 应为 60-120 秒，RPO=0 |
| **存储故障** | 对于任何与存储相关的问题（例如磁盘故障或物理块损坏），应用程序看不到任何影响。 由于数据存储在 3 个副本中，因此将由未发生故障的存储提供数据的副本。 块损坏会自动修复。 如果丢失了数据的副本，则会自动创建数据的新副本。 | 对于不可恢复的错误，灵活服务器会故障转移到备用副本，以减少故障时间。 |
| **逻辑/用户错误** | 在发生用户错误（例如，意外删除了表或错误地更新了数据）后进行的恢复涉及到执行[时间点恢复](concepts-backup-restore.md) (PITR)，方法是将数据还原并恢复到发生错误之前的那个时间点。<br> <br>  如果只需还原部分数据库或特定的表，而不是还原数据库服务器中的所有数据库，则可在新实例中还原数据库服务器，通过 [pg_dump](https://www.postgresql.org/docs/current/app-pgdump.html) 导出表，然后使用 [pg_restore](https://www.postgresql.org/docs/current/app-pgrestore.html) 将这些表还原到数据库中。 | 这些用户错误不受高可用性保护，这是由于所有用户操作均已复制到备用服务器。 |
| **区域故障** | 预览版尚不支持跨区域副本和异地还原功能。 | |

> [!IMPORTANT]
> 已删除的服务器 **无法** 还原。 如果删除服务器，则属于该服务器的所有数据库也会被删除且不可恢复。 使用 [Azure 资源锁](../../azure-resource-manager/management/lock-resources.md)帮助防止意外删除服务器。

## <a name="next-steps"></a>后续步骤

- 了解[备份和恢复](./concepts-backup-restore.md)
