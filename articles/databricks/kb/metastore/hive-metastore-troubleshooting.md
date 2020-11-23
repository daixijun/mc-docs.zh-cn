---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 如何排查多个 Apache Hive 元存储问题 - Azure Databricks
description: 了解如何排查 Apache Hive 元存储问题。
ms.openlocfilehash: d8a9d25cda5af21f09a3bec012d4cef4afe6405f
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589870"
---
# <a name="how-to-troubleshoot-several-apache-hive-metastore-problems"></a>如何排查多个 Apache Hive 元存储问题

## <a name="problem-1-external-metastore-tables-not-available"></a>问题 1：外部元存储表不可用

检查驱动程序日志时，将看到包含错误 `Required table missing` 的堆栈跟踪：

```console
WARN Query: Query for candidates of org.apache.hadoop.hive.metastore.model.MDatabase and subclasses resulted in no possible candidates

Required table missing: "DBS" in Catalog "" Schema "". DataNucleus requires this table to perform its
persistence operations. Either your MetaData is incorrect, or you need to enable
"datanucleus.schema.autoCreateTables"

org.datanucleus.store.rdbms.exceptions.MissingTableException: Required table missing : "DBS" in Catalog ""  Schema "". DataNucleus requires this table to perform its persistence operations. Either your MetaData is incorrect, or you need to enable
"datanucleus.schema.autoCreateTables"

   at

org.datanucleus.store.rdbms.table.AbstractTable.exists(AbstractTable.java:606)

   at

org.datanucleus.store.rdbms.RDBMSStoreManager$ClassAdder.performTablesValidation(RDBMSStoreManager.java:33
85)
```

### <a name="cause"></a>原因

此数据库存在，但没有元存储表。

### <a name="solution"></a>解决方案

如果外部元存储版本为 Hive 2.0 或更高版本，请使用 [Hive 架构工具](https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool)创建元存储表。 对于低于 Hive 2.0 的版本，请在现有初始化脚本中添加具有以下配置的元存储表：

```ini
spark.hadoop.datanucleus.autoCreateSchema=true
spark.hadoop.datanucleus.fixedDatastore=false
```

还可以直接在 Apache [Spark 配置](/databricks/clusters/configure#spark-config) 中设置这些配置：

```ini
datanucleus.autoCreateSchema true
datanucleus.fixedDatastore false
```

## <a name="problem-2-hive-metastore-verification-failed"></a>问题 2：Hive 元存储验证失败

检查驱动程序日志时，将看到包含如下错误的堆栈跟踪：

```console
18/09/24 14:51:07 ERROR RetryingHMSHandler: HMSHandler Fatal error:
MetaException(message:Version information not found in metastore. )

   at
org.apache.hadoop.hive.metastore.ObjectStore.checkSchema(ObjectStore
.java:7564)

   at
org.apache.hadoop.hive.metastore.ObjectStore.verifySchema(ObjectStore.
java:7542)

   at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
```

### <a name="cause"></a>原因

元存储中的 `VERSION` 表为空。

### <a name="solution"></a>解决方案

执行下列操作之一：

* 通过 `INSERT` 查询，用正确的版本值填充 `VERSION` 表。
* 设置以下配置，在群集的 Spark 配置中关闭元存储验证：

  ```ini
  hive.metastore.schema.verification false
  hive.metastore.schema.verification.record.version false
  ```

## <a name="problem-3-metastore-connection-limit-exceeded"></a>问题 3：超过了元存储连接限制

在群集上运行的命令将失败，并在驱动程序日志中出现以下堆栈跟踪：

```console
Unable to open a test connection to the given
database. JDBC url =
jdbc:<jdbcURL>?trustServerCertificate=true&useSS
L=true, username = <REDACTED>. Terminating
connection pool (set lazyInit to true if you
expect to start your database after your app).
Original Exception: ------

java.sql.SQLSyntaxErrorException: User
'<userId>' has exceeded the
'max_user_connections' resource (current value:
100)
at
org.mariadb.jdbc.internal.util.exceptions.Except
ionMapper.get(ExceptionMapper.java:163)
at
org.mariadb.jdbc.internal.util.exceptions.Except
ionMapper.getException(ExceptionMapper.java:106)
at
org.mariadb.jdbc.internal.protocol.AbstractConne
ctProtocol.connectWithoutProxy(AbstractConnectPr
otocol.java:1036)
```

### <a name="cause"></a>原因

元存储配置仅允许 100 个连接。 达到连接限制后，将不允许新的连接，命令将失败并出现此错误。 Azure Databricks 工作区中的每个群集都会建立一条与元存储的连接。 如果有大量群集正在运行，则可能出现此问题。 此外，不正确的配置会导致连接泄露，从而导致连接数不断增加，直到达到限制。

### <a name="solution"></a>解决方案

执行以下操作之一更正此错误：

* 如果使用的是外部元存储并且有大量群集正在运行，则在外部元存储上增加连接限制。
* 如果使用的不是外部元存储，请确保群集上没有任何自定义的 Hive 元存储配置。 使用 Azure Databricks 提供的元存储时，应在群集上对 Hive 元存储使用默认配置。
* 如果使用的是默认配置，但仍遇到此问题，请联系 Azure Databricks 支持人员。 根据 Azure Databricks 工作区的配置，可能会增加允许的内部元存储连接数。

## <a name="problem-4-table-actions-fail-because-column-has-too-much-metadata"></a>问题 4：由于列的元数据太多，表操作失败

当单列的元数据数量超过 4000 个字符时，表操作将失败并出现如下错误：

```console
Error in SQL statement: IllegalArgumentException:
Error: type expected at the position 3998 of 'struct<num_ad_accounts:bigint,num_benchmarks:bigint,num_days_range:string,num_days_in_history:string,num_fb_pages:bigint,num_g_profiles:bigint,num_ga_views:bigint,num_groups:bigint,num_ig_profiles:bigint,num_li_pages:bigint,num_labels:string,num_labels_added:bigint,num_labels_
```

### <a name="cause"></a>原因

这是在 Hive 元存储版本 2.3.0 ([HIVE-12274](https://issues.apache.org/jira/browse/HIVE-12274)) 中修复的 bug。 Azure Databricks 使用 Hive 元存储的早期版本（版本 0.13），因此当某个列（如导入的 JSON 架构）的元数据太多时，就会出现此 bug。

### <a name="solution"></a>解决方案

一种解决方法是设置使用 2.3.0 或更高版本的[外部 Hive 元存储](/databricks/data/metastores/external-hive-metastore)。 然后，使用以下命令删除现有表：

```scala
spark.sessionState
  .catalog
  .externalCatalog
  .dropTable("default", "test_table_tabledrop_1", ignoreIfNotExists = false, purge = false)
```