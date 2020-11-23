---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 03/10/2020
title: Python 2 停用状态 - Azure Databricks
description: 了解 Azure Databricks 中 Python 2 的停用状态。
category: Spark Streaming
author: adampavDB
db-author: adam.pavlacka@databricks.com
ms.openlocfilehash: 4bad0303a5280660fe86720aa4f6ee0ecc8e4d6e
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589538"
---
# <a name="python-2-sunset-status"></a>Python 2 停用状态

Python.org 已于 2020 年 1 月 1 日正式[将 Python 2 转入 EoL（生命周期结束）状态](https://www.python.org/doc/sunset-python-2/)。

这是什么意思呢？

## <a name="databricks-runtime-60-and-above"></a>Databricks Runtime 6.0 及更高版本

Databricks Runtime 6.0 及更高版本仅支持 Python 3。 不能使用这些运行时通过 Python 2 创建群集。 使用这些运行时创建的任何群集都使用 Python 3。

## <a name="databricks-runtime-55-lts"></a>Databricks Runtime 5.5 LTS

使用工作区 UI 创建 Databricks Runtime 5.5 LTS 群集时，默认值为 Python 3。 你可以选择指定 Python 2。 如果使用 [Databricks REST API](/databricks/dev-tools/api/latest/clusters) 创建使用 Databricks Runtime 5.5 LTS 的群集，默认值为 Python 2。 如果有运行 Python 2 的 Databricks Runtime 5.5 LTS 群集，则无需升级到 Python 3。

使用 Databricks REST API 创建群集时，可以使用以下调用来指定 Python 3。

```
"spark_env_vars": {
  "PYSPARK_PYTHON": "/databricks/python3/bin/python3"
},
```

## <a name="should-i-upgrade-to-python-3"></a>是否应该升级到 Python 3？

是否升级取决于你的具体情况，包括对其他系统的依赖关系和依赖项。 应与工程组织一起决定是否升级。

官方 Python.org 语句如下所示：

从 2020 年 1 月 1 日起，不会对 Python 2 进行任何新的 bug 报告、修复或更改，且不再支持 Python 2。我们尚未发布 Python 2.7.17（2019 年 10 月 19 日）的发布之日到 1 月 1 日期间所做的一些更改。我们将捆绑这些修补程序（仅这些修补程序）并发布一个 2.7.18，作为一项社区服务。我们计划在 2020 年 4 月发布，因为这对发布经理来说很方便，但它与支持何时结束无关。

## <a name="support"></a>支持

Azure Databricks 不为已停用的第三方软件提供官方支持。

与 Python 2 相关的支持请求无法获得工程支持。