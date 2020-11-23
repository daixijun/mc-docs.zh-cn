---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 按阶段增加任务数 - Azure Databricks
description: 了解在将 spark xml 包与 Azure Databricks 一起使用时，如何按阶段增加任务数。
ms.openlocfilehash: 274df57257b1f7fae617c8fb56e5dd5a92b43f73
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589549"
---
# <a name="increase-the-number-of-tasks-per-stage"></a>按阶段增加任务数

使用 `spark-xml` 包时，可在群集的 [Spark 配置](/databricks/clusters/configure#spark-config)中将配置设置 `spark.hadoop.mapred.max.split.size` 的值调低，按阶段来增加任务数。 此配置设置会控制输入块大小。 从 DBFS 读取数据时，会将数据分为输入块，然后将其发送到不同的执行程序。 此配置会控制这些输入块的大小。 默认为 128 MB（128000000 字节）。

在笔记本中使用 `spark.conf.set()` 设置此值是无效的。

在以下示例中，“Spark Config”字段显示输入块大小为 32 MB。

> [!div class="mx-imgBorder"]
> ![no-alternative-text](../_static/images/execution/inc-tasks-per-stage.png)