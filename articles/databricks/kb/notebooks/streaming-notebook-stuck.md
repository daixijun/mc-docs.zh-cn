---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 在取消流式处理单元后无法运行笔记本命令 - Azure Databricks
description: 了解在取消流式处理单元后无法运行 Azure Databricks 笔记本命令时要执行的操作。
ms.openlocfilehash: e32a78134d7cdcd37b49b51fc1b52e5c83fa8184
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589546"
---
# <a name="cannot-run-notebook-commands-after-canceling-streaming-cell"></a><a id="cannot-run-notebook-commands-after-canceling-streaming-cell"> </a><a id="streaming-notebook-stuck"> </a>在取消流式处理单元后无法运行笔记本命令

## <a name="problem"></a>问题

在附加到 Databricks Runtime 5.0 群集的笔记本中取消正在运行的流式处理单元后，无法在笔记本中运行任何后续命令。 这些命令将停留在“等待运行”状态，而且你必须清除笔记本的状态，或者分离再重新附加群集，然后才能在笔记本上成功运行命令。

请注意，仅当取消单个单元时，才会出现此问题。当运行所有单元并取消所有单元时，不会出现此问题。

## <a name="version"></a>Version

此问题会影响 Databricks Runtime 5.0 群集。 它还会影响其 Spark 配置 `spark.databricks.chauffeur.enableIdleContextTracking` 已设置为 `true` 的 Databricks Runtime 4.3 群集。

## <a name="cause"></a>原因

Databricks Runtime 4.3 引入了可选的空闲执行上下文功能（在 Databricks Runtime 5.0 中默认启用），该功能允许执行上下文跟踪流式处理执行顺序来确定它们是否处于空闲状态。 遗憾的是，这会导致在你取消流式处理单元时，基础执行上下文保留在无效状态。 在笔记本状态被重置之前，这会阻止其他命令运行。 此行为特定于交互式笔记本，不会影响作业。

有关空闲执行上下文的详细信息，请参阅[执行上下文](/databricks/notebooks/notebooks-manage#execution-context)。

## <a name="solution"></a>解决方案

Azure Databricks 正在努力解决此问题和发布 Databricks Runtime 5.0 的维护更新。 在此期间，你可执行以下任一操作：

* 若要修正受影响的笔记本但不重启群集，请转到笔记本的“清除”菜单，然后选择“清除状态” ：

  > [!div class="mx-imgBorder"]
  > ![no-alternative-text](../_static/images/notebooks/clear-state.png)

* 如果重启群集是可接受的行为，则可通过关闭空闲上下文跟踪来解决此问题。 在群集上设置以下 [Spark 配置](/databricks/clusters/configure#spark-config)值：

  ```bash
  spark.databricks.chauffeur.enableIdleContextTracking false
  ```

  然后，重启该群集。