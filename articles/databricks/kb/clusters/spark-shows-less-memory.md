---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: Apache Spark UI 显示的节点内存量少于总节点内存量 - Azure Databricks
description: 了解当 Spark UI 显示的内存量少于节点上实际可用的内存量时要执行的操作。
ms.openlocfilehash: e0473164490fd25ecee679fe90ae5a5c1f792fd0
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589719"
---
# <a name="apache-spark-ui-shows-less-than-total-node-memory"></a>Apache Spark UI 显示的节点内存量少于总节点内存量

## <a name="problem"></a>问题

Spark UI 中的“执行程序”选项卡显示的内存量少于节点上实际可用的内存量：

* 驱动程序节点的 F8s 实例（16 GB，4 核），在“执行程序”选项卡上显示 `4.5 GB` 内存
* 驱动程序节点的 F4s 实例（8 GB，4 核），在“执行程序”选项卡上显示 `710 GB` 内存：

  > [!div class="mx-imgBorder"]
  > ![no-alternative-text](../_static/images/clusters/spark-memory-ui-azure.png)

## <a name="cause"></a>原因

显示的内存总量小于群集上的内存量，因为某些内存由内核和节点级服务占用。

## <a name="solution"></a>解决方案

若要计算可用的内存量，可以使用用于执行程序内存分配的公式 `(all_memory_size * 0.97 - 4800MB) * 0.8`，其中：

* 0.97 计入了内核开销。
* 4800 MB 计入了内部节点级服务（节点守护程序、日志守护程序等）。
* 0.8 是一个试探性的系数，用于确保运行 Spark 进程的 LXC 容器不会由于“内存不足”错误而崩溃。

F4s 实例上用于存储的可用内存总量为 `(8192MB * 0.97 - 4800MB) * 0.8 - 1024` = 1.2 GB。 因为参数 `spark.memory.fraction` 默认为 0.6，所以，大约 `(1.2 * 0.6)` = ~ 710 MB 是可用于存储的内存量。

可以更改 `spark.memory.fraction` [Spark 配置](https://docs.databricks.com/clusters/configure.html#spark-configuration)以调整此参数。 针对新参数计算可用内存，如下所示：

1. 如果使用的是具有 8192 MB 内存的 F4s 实例，则它具有可用内存 1.2 GB。
2. 如果将 `spark.memory.fraction` 指定为 0.8，则 Spark UI 中的“执行程序”选项卡应显示：`(1.2 * 0.8)` GB = ~960 MB。