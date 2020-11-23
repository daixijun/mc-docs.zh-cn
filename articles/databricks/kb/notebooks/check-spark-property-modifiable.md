---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 如何检查是否可以在笔记本中修改某个 Spark 属性 - Azure Databricks
description: 了解如何在 Azure Databricks 笔记本中修改 Spark 属性。
ms.openlocfilehash: aa717233b11f271549390a5ea398793852fa91d7
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589848"
---
# <a name="how-to-check-if-a-spark-property-is-modifiable-in-a-notebook"></a>如何检查是否可以在笔记本中修改某个 Spark 属性

## <a name="problem"></a>问题

可以通过设置各种配置来优化应用程序。 有些配置必须在群集级别设置，而有些则在笔记本或应用程序内部设置。

## <a name="solution"></a>解决方案

要检查是否可以在笔记本中设置特定的 Spark 配置，请在笔记本单元中运行以下命令：

```scala
spark.conf.isModifiable("spark.databricks.preemption.enabled")
```

如果返回 `true`，则可以在笔记本中设置该属性。 否则，必须在群集级别设置该属性。