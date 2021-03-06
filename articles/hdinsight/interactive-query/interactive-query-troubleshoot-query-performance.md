---
title: Azure HDInsight 中 Apache Hive LLAP 查询的性能不佳
description: Apache Hive LLAP 中的查询执行速度低于预期。
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: v-yiso
origin.date: 07/30/2019
ms.date: 11/11/2019
ms.openlocfilehash: 50832e95c0cb779f15ca4ab322875ce93731d620
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96300864"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>方案：Azure HDInsight 中 Apache Hive LLAP 查询的性能不佳

本文介绍在 Azure HDInsight 群集中使用交互式查询组件时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

默认群集配置未针对工作负荷进行充分优化。 Hive LLAP 中的查询执行速度低于预期。

## <a name="cause"></a>原因

各种原因可能会导致发生此情况。

## <a name="resolution"></a>解决方法

LLAP 已针对涉及联接和聚合的查询进行了优化。 如下所示的查询在交互式 Hive 群集中无法正常运行：

```
select * from table where column = "columnvalue"
```

若要提高 Hive LLAP 中的点查询性能，请设置以下配置：

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

还可以通过以下配置更改增加 LLAP 缓存的使用率以提高性能：

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]