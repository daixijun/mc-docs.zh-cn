---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 05/19/2020
title: 中断后在覆盖模式下创建表失败 - Azure Databricks
description: 了解如何排查在通过取消当前正在运行的作业来重新运行 Apache Spark 写入操作时发生的故障。
ms.openlocfilehash: dbbffd6a483f54ccc406523794bb5f4824aa448d
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589966"
---
# <a name="create-table-in-overwrite-mode-fails-when-interrupted"></a>中断后在覆盖模式下创建表失败

## <a name="problem"></a>问题

在尝试通过取消当前正在运行的作业来重新运行 Apache Spark 写入操作时，会发生以下错误：

```console
Error: org.apache.spark.sql.AnalysisException: Cannot create the managed table('`testdb`.` testtable`').
The associated location ('dbfs:/user/hive/warehouse/testdb.db/metastore_cache_ testtable) already exists.;
```

## <a name="version"></a>Version

此问题可能会在 Databricks Runtime 5.0 及更高版本中发生。

## <a name="cause"></a>原因

此问题的原因是 2.4 版 Spark 的默认行为发生了更改。

如果存在以下情况，则可能发生此问题：

* 正在进行写入操作时终止群集。
* 发生临时性网络问题。
* 作业中断。

某个特定表的元存储数据在损坏后很难恢复，除非通过手动删除该位置中的文件。 从根本上来说，问题在于在 Azure Databricks 尝试覆盖被称为 `_STARTED` 的元数据目录时，该目录不会被自动删除。

可以通过执行以下步骤来重现该问题：

1. 创建数据帧：

   `val df = spark.range(1000)`

2. 在覆盖模式下将该数据帧写入某个位置：

   `df.write.mode(SaveMode.Overwrite).saveAsTable("testdb.testtable")`

3. 在该命令正在执行时将其取消。
4. 重新运行 `write` 命令。

## <a name="solution"></a>解决方案

将 `spark.sql.legacy.allowCreatingManagedTableUsingNonemptyLocation` 标志设置为 `true`。 此标志会删除 `_STARTED` 目录，并会将该进程返回到原始状态。
例如，可以在笔记本中设置该标志：

```python
spark.conf.set("spark.sql.legacy.allowCreatingManagedTableUsingNonemptyLocation","true")
```

或者可以将其设置为群集级别的 [Spark 配置](/databricks/clusters/configure#spark-config)：

```ini
spark.sql.legacy.allowCreatingManagedTableUsingNonemptyLocation true
```

可以选择的另一种方法是手动清理错误消息中指定的数据目录。 可以使用 `dbutils.fs.rm` 来执行此操作。

```scala
dbutils.fs.rm("<path-to-directory>", True)
```