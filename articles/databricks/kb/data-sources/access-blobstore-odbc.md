---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 排查对 Azure Data Lake Storage Gen2 进行 JDBC/ODBC 访问时出现的问题 - Azure Databricks
description: 了解如何排查从 Azure Databricks 对 Azure Data Lake Storage Gen2 进行 JDBC/ODBC 访问时出现的问题。
ms.openlocfilehash: e670ee30d61f140c994420626a2adee5cdb3119e
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589562"
---
# <a name="troubleshooting-jdbcodbc-access-to-azure-data-lake-storage-gen2"></a>排查对 Azure Data Lake Storage Gen2 进行 JDBC/ODBC 访问时出现的问题

## <a name="problem"></a>问题

> [!NOTE]
>
> 通常，如果要访问 Azure Data Lake Storage Gen2 (ADLS Gen2)，应使用包含内置 Azure Blob File System (ABFS) 驱动程序的 Databricks Runtime 5.2 及更高版本。 本文适用于使用 JDBC/ODBC 访问 ADLS Gen2 存储的用户。

从 JDBC 或 ODBC 客户端运行 SQL 查询以访问 ADLS Gen2 时，将发生以下错误：

```console
com.google.common.util.concurrent.UncheckedExecutionException: java.lang.IllegalArgumentException: No value for dfs.adls.oauth2.access.token.provider found in conf file.

18/10/23 21:03:28 ERROR SparkExecuteStatementOperation: Error executing query, currentState RUNNING,
java.util.concurrent.ExecutionException: java.io.IOException: There is no primary group for UGI (Basic token)chris.stevens+dbadmin (auth:SIMPLE)
  at com.google.common.util.concurrent.AbstractFuture$Sync.getValue(AbstractFuture.java:299)
  at com.google.common.util.concurrent.AbstractFuture$Sync.get(AbstractFuture.java:286)
  at com.google.common.util.concurrent.AbstractFuture.get(AbstractFuture.java:116)
  at com.google.common.util.concurrent.Uninterruptibles.getUninterruptibly(Uninterruptibles.java:135)
  at com.google.common.cache.LocalCache$Segment.getAndRecordStats(LocalCache.java:2344)
  at com.google.common.cache.LocalCache$Segment.loadSync(LocalCache.java:2316)
  at com.google.common.cache.LocalCache$Segment.lockedGetOrLoad(LocalCache.java:2278)
  at com.google.common.cache.LocalCache$Segment.get(LocalCache.java:2193)
  at com.google.common.cache.LocalCache.get(LocalCache.java:3932)
  at com.google.common.cache.LocalCache$LocalManualCache.get(LocalCache.java:4721)
  at org.apache.spark.sql.catalyst.catalog.SessionCatalog.getCachedPlan(SessionCatalog.scala:158)
  at org.apache.spark.sql.execution.datasources.FindDataSourceTable.org$apache$spark$sql$execution$datasources$FindDataSourceTable$$readDataSourceTable(DataSourceStrategy.scala:257)
  at org.apache.spark.sql.execution.datasources.FindDataSourceTable$$anonfun$apply$2.applyOrElse(DataSourceStrategy.scala:313)
  at
  at scala.collection.LinearSeqOptimized$class.foldLeft(LinearSeqOptimized.scala:124)
  at scala.collection.immutable.List.foldLeft(List.scala:84)
  at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1.apply(RuleExecutor.scala:87)
  at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1.apply(RuleExecutor.scala:79)
```

从 SQL 客户端运行查询时，你将收到以下错误：

```console
An error occurred when executing the SQL command:
select * from test_databricks limit 50

[Simba][SparkJDBCDriver](500051) ERROR processing query/statement. Error Code: 0, SQL state: com.google.common.util.concurrent.UncheckedExecutionException: com.databricks.backend.daemon.data.common.InvalidMountException: Error while using path /mnt/crm_gen2/phonecalls for resolving path '/phonecalls' within mount at '/mnt/crm_gen2'., Query: SELECT * FROM `default`.`test_databricks` `default_test_databricks` LIMIT 50. [SQL State=HY000, DB Errorcode=500051]

Warnings:
[Simba][SparkJDBCDriver](500100) Error getting table information from database.
```

## <a name="cause"></a>原因

根本原因是配置设置不正确，无法通过 ADLS Gen2 创建 JDBC 或 ODBC 与 ABFS 的连接，从而导致查询失败。

## <a name="solution"></a>解决方案

在群集配置设置中将 `spark.hadoop.hive.server2.enable.doAs` 设置为 `false`。