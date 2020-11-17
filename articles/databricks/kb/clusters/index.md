---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/29/2020
title: 群集 - 提示和疑难解答 - Azure Databricks
ms.openlocfilehash: 8a181c16b1bad104398cb3842ad10851d6d427d8
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589887"
---
# <a name="clusters-tips-and-troubleshooting"></a>群集：提示和疑难解答

这些文章介绍如何管理 Spark 群集。

* [如何计算群集中的核心数](calculate-number-of-cores.md)
* [CPU 内核限制阻止创建群集](azure-core-limit.md)
* [IP 地址限制阻止创建群集](azure-ip-limit.md)
* [群集启动缓慢，缺少节点](azure-nodes-not-acquired.md)
* [群集无法启动](cluster-failed-launch.md)
* [作业因群集管理器核心实例请求限制而失败](cluster-manager-limit.md)
* [管理员用户无法重启群集来运行作业](cluster-restart-fails-admin-user.md)
* [由于 Ganglia 指标填充根分区，导致群集速度缓慢](slowdown-from-root-disk-fill.md)
* [设置执行程序日志级别](set-executor-log-level.md)
* [群集意外终止](termination-reasons.md)
* [如何在 Azure Databricks 群集上覆盖 `log4j` 配置](overwrite-log4j-logs.md)
* [添加配置设置会覆盖所有默认的 `spark.executor.extraJavaOptions` 设置](conf-overwrites-default-settings.md)
* [Apache Spark 执行程序内存分配](spark-executor-memory.md)
* [Apache Spark UI 显示的节点内存量少于总节点内存量](spark-shows-less-memory.md)
* [将群集配置为使用自定义 NTP 服务器](use-internal-ntp.md)