---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/26/2020
title: 流式传输 - 提示和疑难解答 - Azure Databricks
ms.openlocfilehash: b9371ade10088fccd00aedf64f9d9bcdedd7ad16
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589570"
---
# <a name="streaming-tips-and-troubleshooting"></a>流式传输：提示和疑难解答

这些文章介绍结构化流式处理和 Spark 流式处理（旧版 Spark 流式处理功能）。

* [使用文件接收器进行流式处理：在更改检查点或输出目录的情况下出现恢复问题](file-sink-streaming.md)
* [如何从上次写入的偏移位置重启结构化流式处理查询](ss-read-from-last-offset.md)
* [使用 `display()` 时，检查点文件不会被删除](checkpoint-no-cleanup-display.md)
* [使用 `foreachBatch()` 时，检查点文件不会被删除](checkpoint-no-cleanup-foreachbatch.md)