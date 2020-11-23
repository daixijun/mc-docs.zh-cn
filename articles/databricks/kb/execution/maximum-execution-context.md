---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 达到了执行上下文数目上限或附加的笔记本数目上限 - Azure Databricks
description: 了解在 Azure Databricks 中达到执行上下文数目上限或附加的笔记本数目上限时要执行的操作。
ms.openlocfilehash: 3e951110708f0301577610234493cf21a0df4d5d
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589461"
---
# <a name="maximum-execution-context-or-notebook-attachment-limit-reached"></a>达到了执行上下文数目上限或附加的笔记本数目上限

## <a name="problem"></a>问题

笔记本或作业执行将停止，并返回以下错误之一：

```console
Run result unavailable: job failed with error message
Context ExecutionContextId(1731742567765160237) is disconnected.
```

```console
Can’t attach this notebook because the cluster has reached the attached notebook limit. Detach a notebook and retry.
```

## <a name="cause"></a>原因

将笔记本附加到群集时，Azure Databricks 会创建[执行上下文](/databricks/notebooks/notebooks-manage#execution-context)。 如果附加到某个群集的笔记本过多或创建了太多的作业，则在某个时间点，该群集会达到最大阈值限制（145 个执行上下文），Azure Databricks 将返回错误。

## <a name="solution"></a>解决方案

[配置上下文自动逐出](/databricks/notebooks/notebooks-manage#configure-context-auto-eviction)，这允许 Azure Databricks 删除（逐出）空闲的执行上下文。 此外，从管道和 ETL 设计的角度来看，你可以通过以下方法来避免此问题：

* 使用较少的笔记本以减少创建的执行上下文数目。
* 使用作业群集而不是交互式群集。 如果用例允许，将笔记本或 jar 作为作业提交。