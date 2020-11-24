---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 追加到数据帧 - Azure Databricks
description: 了解如何追加到 Azure Databricks 中的数据帧。
ms.openlocfilehash: 91399fc0a60a4cfca26921095b6dc0c2534258cc
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589607"
---
# <a name="append-to-a-dataframe"></a>追加到数据帧

若要追加到数据帧，请使用 `union` 方法。

```scala
%scala

val firstDF = spark.range(3).toDF("myCol")
val newRow = Seq(20)
val appended = firstDF.union(newRow.toDF())
display(appended)
```

```python
%python

firstDF = spark.range(3).toDF("myCol")
newRow = spark.createDataFrame([[20]])
appended = firstDF.union(newRow)
display(appended)
```