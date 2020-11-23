---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 09/22/2020
title: 访问 MLflow 试验项目时出现 OSError 错误 - Azure Databricks
description: 解决尝试访问、下载或记录 MLflow 试验项目时出现的 OSError 错误。
category: Machine Learning
author: arjundc-db
db-author: arjun.dcunha@databricks.com
ms.openlocfilehash: dc6c65ad21aee862dc35ce09af08d62b92750df9
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589660"
---
# <a name="oserror-when-accessing-mlflow-experiment-artifacts"></a>访问 MLflow 试验项目时出现 `OSError` 错误

## <a name="problem"></a>问题

尝试使用以下某项下载或记录项目时收到 `OSError: No such file or directory` 错误消息：

* `MlflowClient.download_artifacts()`
* `mlflow.[flavor].log_model()`
* `mlflow.[flavor].load_model()`
* `mlflow.log_artifacts()`

```console
OSError: No such file or directory: '/dbfs/databricks/mlflow-tracking/<experiment-id>/<run-id>/artifacts/...'
```

## <a name="cause"></a>原因

你的 MLflow 客户端已过期。

MLflow 的早期版本不提供对 `dbfs:/databricks/mlflow-tracking/` 中存储的项目的支持。

## <a name="solution"></a>解决方案

请升级到 MLflow 版本1.9.1 或更高版本，然后重试。

```bash
%sh
pip install --upgrade mlflow
```