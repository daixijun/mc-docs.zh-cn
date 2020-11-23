---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: Delta Lake UPDATE 查询失败，出现 IllegalState 异常 - Azure Databricks
description: 了解如何解决使用 Python UDF 的 Delta Lake UPDATE、DELETE 或 MERGE 查询出现的问题。
ms.openlocfilehash: 1472780de4ca5db859a3118d2444089668c4cdd9
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589605"
---
# <a name="delta-lake-update-query-fails-with-illegalstate-exception"></a>Delta Lake `UPDATE` 查询失败，出现 `IllegalState` 异常

## <a name="problem"></a>问题

执行在其任何转换中使用 Python UDF 的 Delta Lake `UPDATE`、`DELETE` 或 `MERGE` 查询时，执行将失败并出现以下异常：

```console
java.lang.UnsupportedOperationException: Error in SQL statement:
IllegalStateException: File (adl://xxx/table1) to be rewritten not found among candidate files:
adl://xxx/table1/part-00001-39cae1bb-9406-49d2-99fb-8c865516fbaa-c000.snappy.parquet
```

## <a name="version"></a>Version

此问题发生在 Databricks Runtime 5.5 及更低版本上。

## <a name="cause"></a>原因

Delta Lake 内部依赖于 `input_file_name()` 函数来执行 `UPDATE`、`DELETE` 和 `MERGE` 之类的操作。 如果在用于评估 Python UDF 的 `SELECT` 语句中使用 `input_file_name()`，则会返回空值。 `UPDATE` 在内部调用 `SELECT`，这样将无法返回文件名并导致错误。 Scala UDF 不会发生此错误。

## <a name="solution"></a>解决方案

可以使用两个选项：

* 使用 Databricks Runtime 6.0 或更高版本，它们包括此问题的解决方法：[[SPARK-28153]](https://issues.apache.org/jira/browse/SPARK-28153)。
* 如果无法使用 Databricks Runtime 6.0 或更高版本，请使用 Scala UDF 而不是 Python UDF。