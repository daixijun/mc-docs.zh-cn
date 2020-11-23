---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 如何填充或更新现有 Delta 表中的列 - Azure Databricks
description: 了解如何填充或更新现有 Delta 表中的列。
ms.openlocfilehash: 2a2f5a9153162dc1a8002a66eb753425b169b754
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589557"
---
# <a name="how-to-populate-or-update-columns-in-an-existing-delta-table"></a>如何填充或更新现有 Delta 表中的列

## <a name="problem"></a>问题

你有一个现有的 Delta 表，其中包含几个空白列。 你需要使用原始 Parquet 文件中的数据填充或更新这些列。

## <a name="solution"></a>解决方案

在此示例中，有一个 `customers` 表，它是现有的 Delta 表。 该表包含具有缺失值的 address 列。 更新后的数据以 Parquet 格式存在。

1. 使用 Apache Spark API 语句从 Parquet 文件创建 `DataFrame`：

   ```python
   updatesDf = spark.read.parquet("/path/to/raw-file")
   ```

2. 查看 `updatesDF` `DataFrame` 的内容：

   ```python
   display(updatesDf)
   ```

3. 从 `updatesDf` `DataFrame` 创建表。 在此示例中，该类名为 `updates`。

   ```python
   updatesDf.createOrReplaceTempView("updates")
   ```

4. 检查 updates 表的内容，并将其与 `customers` 的内容进行比较：

   ```python
   display(customers)
   ```

5. 使用 `MERGE INTO` 语句将 `updates` 表中的数据合并到原始 `customers` 表中。

   ```sql
   MERGE INTO customers
   USING updates
   ON customers.customerId = source.customerId
   WHEN MATCHED THEN
     UPDATE SET address = updates.address
   WHEN NOT MATCHED
     THEN INSERT (customerId, address) VALUES (updates.customerId, updates.address)
   ```

这里，`customers` 是原始 Delta 表，包含具有缺失值的 `address` 列。 `updates` 是从 `DataFrame` `updatesDf` 创建的表，通过读取原始文件中的数据创建的。 原始 Delta 表的 `address` 列使用 `updates` 中的值进行填充，并覆盖 `address` 列中的任何现有值。

如果 `updates` 包含 `customers` 表中尚未包含的客户，则该命令将添加这些新客户记录。

有关使用 `MERGE INTO` 的更多示例，请参阅[合并到 (Delta Lake)](/databricks/spark/latest/spark-sql/language-manual/merge-into)。