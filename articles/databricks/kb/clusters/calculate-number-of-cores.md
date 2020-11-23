---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 如何计算群集中的核心数 - Azure Databricks
description: 了解如何计算 Azure Databricks 群集中的核心数。
ms.openlocfilehash: 1aad1998c2c8d7cb122deb110e1507690108f16d
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589799"
---
# <a name="how-to-calculate-the-number-of-cores-in-a-cluster"></a>如何计算群集中的核心数

如果组织已在群集节点上安装指标服务，则可以使用群集详细信息页上的[指标](/databricks/clusters/clusters-manage#monitor-performance)选项卡在工作区 UI 中查看 Azure Databricks 群集中的核心数。

如果驱动程序和执行程序的节点类型相同，则还可以使用 Scala 实用程序代码以编程方式确定群集中可用的核心数：

1. 使用 `sc.statusTracker.getExecutorInfos.length` 获取节点总数。 结果包括驱动程序节点，因此减 1。
2. 使用 `java.lang.Runtime.getRuntime.availableProcessors` 获取每个节点的核心数。
3. 将两个结果相乘（从节点总数中减去 1）得到可用总核心数：

   ```scala
   java.lang.Runtime.getRuntime.availableProcessors * (sc.statusTracker.getExecutorInfos.length -1)
   ```