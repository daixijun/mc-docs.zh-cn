---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 区分活动作业与不活动作业 - Azure Databricks
description: 了解如何区分活动和不活动的 Azure Databricks 作业。
ms.openlocfilehash: 66db54a075a229b98a67728d9894c3b222104806
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589448"
---
# <a name="distinguish-active-and-dead-jobs"></a>区分活动作业与不活动作业

本文介绍如何区分活动作业和不活动作业。

## <a name="problem"></a>问题

在具有太多并发作业的群集上，经常会看到某些作业卡在 Spark UI 中，进度卡滞不前。 这会使活动作业/阶段和不活动作业/阶段的识别变得复杂。

> [!div class="mx-imgBorder"]
> ![no-alternative-text](../_static/images/jobs/stuck-stages.png)

## <a name="cause"></a>原因

每当群集上运行的并发作业过多时，Spark 内部 `eventListenerBus` 都可能会删除事件。 这些事件用于在 Spark UI 中跟踪作业进度。 每当事件侦听器删除事件时，都会在 Spark UI 中看到永远无法完成的不活动作业/阶段。 作业实际上已经完成，但在 Spark UI 中并未显示为“已完成”。

你在驱动程序日志中看到以下跟踪：

```console
18/01/25 06:37:32 WARN LiveListenerBus: Dropped 5044 SparkListenerEvents since Thu Jan 25 06:36:32 UTC 2018
```

## <a name="solution"></a>解决方案

如果不重启群集，就无法从 Spark UI 中删除不活动的作业。 不过，你可以通过运行以下命令来识别活动的作业和阶段：

```scala
sc.statusTracker.getActiveJobIds()  // Returns an array containing the IDs of all active jobs.
sc.statusTracker.getActiveStageIds() // Returns an array containing the IDs of all active stages.
```