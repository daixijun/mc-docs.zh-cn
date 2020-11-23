---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 09/29/2020
title: Apache Spark 执行程序内存分配 - Azure Databricks
description: 了解 Apache Spark 执行程序内存分配在 Azure Databricks 群集中的工作方式。
category: Clusters
author: charlesb
db-author: charles.bernard@databricks.com
ms.openlocfilehash: e1685edef2507ae3d371ef6de6d77507fcecda3c
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589886"
---
# <a name="apache-spark-executor-memory-allocation"></a>Apache Spark 执行程序内存分配

默认情况下，可用于每个执行程序的内存量是在 Java 虚拟机 (JVM) 内存堆中分配的。 这由 `spark.executor.memory` 属性进行控制。

  ![具有 Spark 执行程序内存属性的内存配置](../_static/images/clusters/spark-executor-memory.png)

但是，在分配了大量内存的实例上观察到了一些意外的行为。 随着 JVM 的内存大小纵向扩展，垃圾回收器的问题会变得很明显。 可通过限制垃圾回收器管理下的内存量来解决这些问题。

所选 Azure Databricks 群集类型会启用堆外模式，这会限制垃圾回收器管理下的内存量。 正因如此，某些 Spark 群集将 `spark.executor.memory` 值设置为总体群集内存的一小部分。

  ![具有 spark 内存堆外大小属性的内存配置](../_static/images/clusters/spark-memory-offHeap-size.png)

堆外模式由在 Spark 1.6.0 及更高版本中可用的 `spark.memory.offHeap.enabled` 和 `spark.memory.offHeap.size` 属性控制。

以下 Azure Databricks 群集类型启用了堆外内存策略：

* Standard_L8s_v2
* Standard_L16s_v2
* Standard_L32s_v2
* Standard_L32s_v2
* Standard_L80s_v2