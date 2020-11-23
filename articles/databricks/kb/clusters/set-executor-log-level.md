---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/24/2020
title: 设置执行程序日志级别 - Azure Databricks
description: 了解如何设置 Azure Databricks 执行程序上的日志级别。
ms.openlocfilehash: eb3bdca19cacc421665c55abf2bdd955c15ee11d
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589888"
---
# <a name="set-executor-log-level"></a>设置执行程序日志级别

若要设置所有执行程序上的日志级别，必须在每个辅助角色的 JVM 内进行设置。

例如：

```scala
sc.parallelize(Seq("")).foreachPartition(x => {
  import org.apache.log4j.{LogManager, Level}
  import org.apache.commons.logging.LogFactory

  LogManager.getRootLogger().setLevel(Level.DEBUG)
  val log = LogFactory.getLog("EXECUTOR-LOG:")
  log.debug("START EXECUTOR DEBUG LOG LEVEL")
})
```

若要验证是否已设置级别，请导航到 [Spark UI](/databricks/clusters/clusters-manage#clusters-sparkui)，选择“执行程序”选项卡，然后打开任何执行程序的 `stderr` 日志：

> [!div class="mx-imgBorder"]
> ![no-alternative-text](../_static/images/clusters/change-log-level.gif)