---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: Spark 作业失败，驱动程序暂时不可用 - Azure Databricks
description: 了解由于驱动程序不可用而导致 Azure Databricks 作业失败的原因和解决方案。
ms.openlocfilehash: 2950b0d9d0d738080e85f7d6b09c3ae93b971c3c
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589793"
---
# <a name="spark-job-fails-with-driver-is-temporarily-unavailable"></a>Spark 作业失败，出现错误消息 `Driver is temporarily unavailable`

## <a name="problem"></a>问题

Databricks 笔记本返回以下错误：

```console
Driver is temporarily unavailable
```

问题可能是间歇性的或持续性的。

相关错误消息如下：

```console
Lost connection to cluster. The notebook may have been detached.
```

## <a name="cause"></a>原因

导致此错误的一个常见原因是驱动程序正在经历内存瓶颈。 发生这种情况时，驱动程序会由于内存不足 (OOM) 状况而崩溃并重新启动，或由于频繁的完整垃圾回收变得无响应。 造成内存瓶颈的原因有以下几种：

* 驱动程序实例类型对于在驱动程序上执行的负载不是最佳的。
* 驱动程序上执行内存密集型操作。
* 同一群集上有多个并行运行的笔记本或作业。

## <a name="solution"></a>解决方案

解决方案因情况而异。 在没有具体细节的情况下，最简单的解决方法是增加驱动程序内存。 只需在 Azure Databricks 工作区中的群集编辑页上升级驱动程序节点类型即可增加驱动程序内存。

要考虑的其他要点：

* 避免内存密集型操作，如：
  * `collect()` 运算符，该运算符将大量数据引入驱动程序。
  * 将大型数据帧转换为 Pandas

  如果这些操作是必要的，请确保有足够的驱动程序内存可用。

* 避免在共享的交互式群集上运行批处理作业。
* 将工作负载分配到不同的群集。 无论群集有多大，Spark 驱动程序的功能都不能在群集中分布。