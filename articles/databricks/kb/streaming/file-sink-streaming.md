---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 使用文件接收器进行流式传输 - 在更改检查点或输出目录的情况下出现恢复问题 - Azure Databricks
description: 了解如何解决在使用文件接收器进行流式传输时更改检查点或输出目录的情况下出现的恢复问题。
ms.openlocfilehash: a975cb9cca3ba8db197d7d20dda4f36f79e60932
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589498"
---
# <a name="streaming-with-file-sink-problems-with-recovery-if-you-change-checkpoint-or-output-directories"></a>使用文件接收器进行流式处理：在更改检查点或输出目录的情况下出现恢复问题

将数据流式传输到文件接收器时，应始终将检查点和输出目录一起更改。 否则，可能会造成失败或意外的输出。

Apache Spark 将在名为 `_spark_metadata` 的输出目录中创建一个文件夹。 此文件夹包含每个批运行的预写日志。 这是在写入文件系统时 Spark 获得“恰好一次”保证的方式。  此文件夹包含为每个批保存的文件（名为 `0,1,2,3` 等 + `19.compact`、`n.compact` 等）。 这些文件包括 JSON，以用于提供有关特定批的输出的详细信息。 借助于此数据，一旦某个批已成功，则任何重复的批输出都将被丢弃。

**如果更改检查点目录，而不更改输出目录：**

更改检查点目录时，流作业将再次从 0 开始批处理。 由于 0 已存在于 `_spark_metadata` 文件夹中，因此将丢弃输出文件，即使该文件包含新数据。 也就是说，如果上一次运行是在第 500 批停止的，则具有相同输出目录和不同检查点目录的下一次运行将仅提供针对第 501 批的输出。 之前的所有批都将以静默方式丢弃。

**如果更改输出目录，而不更改检查点目录：**

如果只更改输出目录，则会丢失 `_spark_metadata` 文件夹中的所有批数据。 但 Spark 将按照检查点目录开始从下一批写入。 例如，如果在第 500 批停止了上一次运行，则新的流作业的第一次写入操作将在 `_spark_metadata` 中的文件 501 上，并且你将丢失所有旧批。 当读回文件时，会收到 `metadata for batch 0(or first compact file (19.compact)) is not found` 错误。

> [!div class="mx-imgBorder"]
> ![no-alternative-text](../_static/images/streaming/file-sink-streaming.png)