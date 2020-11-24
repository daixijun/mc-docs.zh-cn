---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 如何在基于数据集和数据帧的联接命令中指定倾斜提示 - Azure Databricks
description: 了解如何在 Azure Databricks 的基于数据集和数据帧的联接命令中指定倾斜提示。
ms.openlocfilehash: 880d04618c1385e9c5021d1517401e340b1b8529
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589753"
---
# <a name="how-to-specify-skew-hints-in-dataset-and-dataframe-based-join-commands"></a>如何在基于数据集和数据帧的联接命令中指定倾斜提示

对 `DataFrame` 或 `Dataset` 对象执行 `join` 命令时，如果发现由于[数据倾斜](https://en.wikipedia.org/wiki/Skewness)而导致查询无法完成少量任务，则可以使用 `hint("skew")` 方法 `df.hint("skew")` 指定倾斜提示。 [倾斜联接优化](/databricks/delta/join-performance/skew-join)将在为其指定了 `skew` 提示的 `DataFrame` 上执行。

除了基本提示外，还可以指定具有以下参数组合的 `hint` 方法：列名、列名列表以及列名和倾斜值。

* `DataFrame` 和列名。 倾斜联接优化将在 `DataFrame` 的指定列上执行。

  ```python
  df.hint("skew", "col1")
  ```

* `DataFrame` 和多个列。 倾斜联接优化将对 `DataFrame` 中的多个列执行。

  ```python
  df.hint("skew", ["col1","col2"])
  ```

* `DataFrame`、列名和倾斜值。 倾斜联接优化将对具有倾斜值的列中的数据执行。

  ```python
  df.hint("skew", "col1", "value")
  ```

## <a name="example"></a>示例

此示例演示了如何为 `join` 操作中涉及的多个 `DataFrame` 对象指定倾斜提示：

```scala
val joinResults = ds1.hint("skew").as("L").join(ds2.hint("skew").as("R"), $"L.col1" === $"R.col1")
```