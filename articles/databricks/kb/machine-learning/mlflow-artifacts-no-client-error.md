---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 09/22/2020
title: 在不使用 MLflow 客户端的情况下访问 MLflow 项目时出错 - Azure Databricks
description: 解决在不使用 MLflow 客户端的情况下尝试访问 MLflow 项目时出错的问题
category: Machine Learning
author: arjundc-db
db-author: arjun.dcunha@databricks.com
ms.openlocfilehash: 170fdc5f99c4cfec57aef281e5f0eb76603d4439
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589658"
---
# <a name="errors-when-accessing-mlflow-artifacts-without-using-the-mlflow-client"></a>在不使用 MLflow 客户端的情况下访问 MLflow 项目时出错

现在，对 MLflow 跟踪中的项目实施了 [MLflow 试验权限](/databricks/security/access-control/workspace-acl#mlflow-artifact-permissions)，使你可以轻松控制对数据集、模型和其他文件的访问。

## <a name="invalid-mount-exception"></a>“无效装载”异常

### <a name="problem"></a>问题

尝试使用 Databricks 文件系统 (DBFS) 命令（如 `dbutils.fs`）访问 MLflow 运行项目时，收到以下错误：

```console
com.databricks.backend.daemon.data.common.InvalidMountException: Error while using path /databricks/mlflow-tracking/<experiment-id>/<run-id>/artifacts for resolving path &#39;/<experiment-id>/<run-id>/artifacts&#39; within mount at &#39;/databricks/mlflow-tracking&#39;.
```

### <a name="cause"></a>原因

随着 MLflow 试验权限扩展到项目，`dbfs:/databricks/mlflow-tracking/` 中存储的运行项目的 DBFS 访问 API 不再受到支持。

### <a name="solution"></a>解决方案

升级到 MLflow 客户端版本 1.9.1 或更高版本，以便下载、列出或上传存储在 `dbfs:/databricks/mlflow-tracking/`中的项目。

```bash
%sh
pip install --upgrade mlflow
```

## <a name="filenotfounderror"></a>FileNotFoundError

### <a name="problem"></a>问题

尝试使用 `%sh`/`os.listdir()` 访问 MLflow 运行项目时，收到以下错误：

```console
FileNotFoundError: [Errno 2] No such file or directory: '/databricks/mlflow-tracking/'
```

### <a name="cause"></a>原因

随着 MLflow 试验权限扩展到项目，只能使用 MLflow 客户端版本 1.9.1 或更高版本访问存储在 `dbfs:/databricks/mlflow-tracking/` 中的运行项目。

### <a name="solution"></a>解决方案

升级到 MLflow 客户端版本 1.9.1 或更高版本，以便下载、列出或上传存储在 `dbfs:/databricks/mlflow-tracking/`中的项目。

```bash
%sh
pip install --upgrade mlflow
```