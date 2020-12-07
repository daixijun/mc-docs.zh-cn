---
title: 还原已删除的专用 SQL 池
description: 有关在 Azure Synapse Analytics 中还原已删除的 SQL 池的指南。
services: synapse-analytics
author: WenJason
manager: digimobile
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
origin.date: 08/29/2018
ms.date: 11/30/2020
ms.author: v-jay
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0c0cef4179d5e6ea4cf9cc0a7c768338bf157ea3
ms.sourcegitcommit: dabbf66e4507a4a771f149d9f66fbdec6044dfbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96153111"
---
# <a name="restore-a-deleted-dedicated-sql-pool-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中还原已删除的专用 SQL 池

本文介绍如何使用 Azure 门户或 PowerShell 还原专用 SQL 池。

## <a name="before-you-begin"></a>准备阶段

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**验证 DTU 容量。** 每个专用 SQL 池都由一个具有默认 DTU 配额的[逻辑 SQL Server](../../azure-sql/database/logical-servers.md)（例如 myserver.database.chinacloudapi.cn）托管。  验证该服务器的剩余 DTU 配额是否足够进行数据库还原。

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>通过 PowerShell 还原已删除的数据仓库

若要还原已删除的专用 SQL 池，请使用 [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) cmdlet。 如果相应的服务器也已被删除，则不能还原该数据仓库。

1. 开始之前，请确保[安装 Azure PowerShell](https://docs.microsoft.com/powershell/azure/?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。
2. 打开 PowerShell。
3. 连接到 Azure 帐户，并列出与帐户关联的所有订阅。
4. 选择包含要还原的已删除专用 SQL 池的订阅。
5. 获取特定的已删除数据仓库。
6. 还原已删除的专用 SQL 池
    * 若要将已删除的专用 SQL 池还原到另一服务器，请确保指定其他服务器名称。  该服务器也可以位于另一资源组和区域中。
7. 验证已还原的数据仓库是否处于联机状态。
8. 完成还原后，可以按[在恢复后配置数据库](../../azure-sql/database/disaster-recovery-guidance.md?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)中的说明配置恢复后的数据仓库。

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.chinacloudapi.cn
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount -Environment AzureChinaCloud
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>通过 Azure 门户还原已删除的数据库

1. 登录到 [Azure 门户](https://portal.azure.cn/)。
2. 导航到承载着已删除数据仓库的服务器。
3. 在目录中选择“已删除的数据库”图标。

    ![已删除的数据库](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. 选择要还原的已删除 Azure Synapse Analytics。

    ![选择“已删除的数据库”](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. 指定新的 **数据库名称**，并单击“确定”

    ![指定数据库名称](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>后续步骤

- [还原现有专用 SQL 池](sql-data-warehouse-restore-active-paused-dw.md)
- [从异地备份专用 SQL 池进行还原](sql-data-warehouse-restore-from-geo-backup.md)
