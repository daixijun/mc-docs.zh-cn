---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 如何处理具有不同架构的已损坏 Parquet 文件 - Azure Databricks
description: 了解如何使用 Azure Databricks 读取具有特定架构的 Parquet 文件。
ms.openlocfilehash: 65ba87bc9ec705be40c908f22c4385d6c4c4af29
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589755"
---
# <a name="how-to-handle-corrupted-parquet-files-with-different-schema"></a>如何处理具有不同架构的已损坏 Parquet 文件

## <a name="problem"></a>问题

假设你有大量基本独立的 Parquet 文件，其中包含各种不同的架构。 你希望只读取与特定架构匹配的文件，而跳过不匹配的文件。

一种解决方案是按顺序读取文件、识别架构，并将 `DataFrames` 合并在一起。 但是，当有数十万个文件时，这种方法会不切实际。

## <a name="solution"></a>解决方案

将 Apache Spark 属性 `spark.sql.files.ignoreCorruptFiles` 设置为 `true`，然后读取包含所需架构的文件。 与指定架构不匹配的文件会被忽略。 生成的数据集仅包含那些与指定架构匹配的文件中的数据。

使用 `spark.conf.set` 设置 Spark 属性：

```bash
spark.conf.set("spark.sql.files.ignoreCorruptFiles", "true")
```

或者，可以在 [Spark 配置](/databricks/clusters/configure#spark-config)中设置此属性。