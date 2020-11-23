---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 07/07/2020
title: 库失败并出现依赖项异常 - Azure Databricks
description: 了解笔记本范围内的库为何触发 Apache Spark 依赖项异常并返回“无法满足要求”错误。
category: Libraries
db-author: jordan.hicks@databricks.com
ms.openlocfilehash: e12b1c66e3c503352685eb1d5e35fdddb91e8f9d
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589960"
---
# <a name="libraries-fail-with-dependency-exception"></a>库失败并出现依赖项异常

## <a name="problem"></a>问题

你有一个 Python 函数是在自定义 egg 或 wheel 文件中定义的，还有依赖项由集群上安装的另一个客户包来满足要求。

调用此函数时，它会返回一个错误，指出无法满足要求。

```console
org.apache.spark.SparkException: Process List(/local_disk0/pythonVirtualEnvDirs/virtualEnv-d82b31df-1da3-4ee9-864d-8d1fce09c09b/bin/python, /local_disk0/pythonVirtualEnvDirs/virtualEnv-d82b31df-1da3-4ee9-864d-8d1fce09c09b/bin/pip, install, fractal==0.1.0, --disable-pip-version-check) exited with code 1. Could not find a version that satisfies the requirement fractal==0.1.0 (from versions: 0.1.1, 0.1.2, 0.2.1, 0.2.2, 0.2.3, 0.2.4, 0.2.5, 0.2.6, 0.2.7, 0.2.8, 0.2.9, 0.3.0)
```

例如，假设你同时安装了 wheel A 和 wheel B，而无论是通过 UI 安装到群集还是通过笔记本范围内的库。 假定 wheel A 在 wheel B 上具有依赖项。

* `dbutils.library.install(/path_to_wheel/A.whl)`
* `dbutils.library.install(/path_to_wheel/B.whl)`

尝试使用上述某一个库进行调用时，你会收到“无法满足要求”错误。

## <a name="cause"></a>原因

即使已通过群集 UI 安装所需的依赖项或通过笔记本范围内的库安装来满足了要求，Azure Databricks 仍然无法保证特定库在群集上的安装顺序。 如果某个库正在被引用，但它却未分发给执行程序节点，那么为了满足要求，它将回退到 PyPI 并在本地使用。

## <a name="solution"></a>解决方案

应使用一个包含所有必需代码和依赖项的 egg 或 wheel 文件。 这可确保代码在运行时加载了正确的库且这些库可供使用。