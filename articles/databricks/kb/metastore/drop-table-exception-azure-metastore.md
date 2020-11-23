---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 在 Azure 支持的元存储上删除表时出现 AnalysisException - Azure Databricks
description: 了解如何解决在 Azure 支持的元存储上删除表时出现的 AnalysisException。
ms.openlocfilehash: 3a892673da11ab92d6868b55104f2f4a9af0aae1
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589872"
---
# <a name="analysisexception-when-dropping-table-on-azure-backed-metastore"></a>在 Azure 支持的元存储上删除表时出现 `AnalysisException`

## <a name="problem"></a>问题

尝试删除部署在 Azure SQL 数据库上的外部 Hive 2.0 或 2.1 版元存储中的表时，Azure Databricks 会引发以下异常：

```console
com.databricks.backend.common.rpc.DatabricksExceptions$SQLExecutionException: org.apache.spark.sql.AnalysisException: org.apache.hadoop.hive.ql.metadata.HiveException: MetaException(message:Exception thrown when executing query : SELECT 'org.apache.hadoop.hive.metastore.model.MStorageDescriptor' AS NUCLEUS_TYPE,A0.INPUT_FORMAT,A0.IS_COMPRESSED,A0.IS_STOREDASSUBDIRECTORIES,A0.LOCATION,A0.NUM_BUCKETS,A0.OUTPUT_FORMAT,A0.SD_ID FROM SDS A0 WHERE A0.CD_ID = ? OFFSET 0 ROWS FETCH NEXT ROW ONLY );
  at org.apache.spark.sql.hive.HiveExternalCatalog.withClient(HiveExternalCatalog.scala:107)
  at org.apache.spark.sql.hive.HiveExternalCatalog.doDropTable(HiveExternalCatalog.scala:483)
  at org.apache.spark.sql.catalyst.catalog.ExternalCatalog.dropTable(ExternalCatalog.scala:122)
  at org.apache.spark.sql.catalyst.catalog.SessionCatalog.dropTable(SessionCatalog.scala:638)
  at org.apache.spark.sql.execution.command.DropTableCommand.run(ddl.scala:212)
```

## <a name="cause"></a>原因

这是一个已知的 Hive bug ([HIVE-14698](https://issues.apache.org/jira/browse/HIVE-14698))，由包中 `datanucleus-rdbms` 模块的另一个已知 bug 引起。 它已在 `datanucleus-rdbms` 4.1.16 中得到修复。 但是，Hive 2.0 和 2.1 元存储使用版本 4.1.7，这些版本会受到影响。

## <a name="solution"></a>解决方案

执行下列操作之一：

* 将 Hive 元存储升级到版本 2.3.0。  这也可解决由于版本 2.3.0 中修复的任何其他 Hive bug 而导致的问题。
* 将以下笔记本导入工作区，然后按照说明替换 `datanucleus-rdbms` JAR。 此笔记本旨在将元存储升级到版本 2.1.1。 你可能想要在服务器端使用类似的版本。

### <a name="external-metastore-upgrade-notebook"></a>外部元存储升级笔记本

[获取笔记本](../_static/notebooks/metastore/external-metastore-hive-2.1.1.html)