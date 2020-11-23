---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 09/22/2020
title: 使用旧的项目存储位置时出现试验警告 - Azure Databricks
description: 解决当使用旧的项目存储位置而不是 MLflow 托管位置时，出现试验警告的问题。
category: Machine Learning
author: arjundc-db
db-author: arjun.dcunha@databricks.com
ms.openlocfilehash: 46988d691eb5358ed1a94efe2d92ba3464908f37
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589722"
---
# <a name="experiment-warning-when-legacy-artifact-storage-location-is-used"></a>使用旧的项目存储位置时出现试验警告

## <a name="problem"></a>问题

MLflow 试验页面上显示一个新图标，其中包含以下打开访问警告：

> [!div class="mx-imgBorder"]
> ![no-alternative-text](../_static/images/machine-learning/legacy-artifact-location-warning.png)

## <a name="cause"></a>原因

现在，会对 MLflow 跟踪中的项目强制执行 [MLflow 试验权限](/databricks/security/access-control/workspace-acl#mlflow-artifact-permissions)，这使你可以轻松控制对数据集、模型和其他文件的访问。

在 MLflow 1.11 及更高版本中，新试验将项目存储在强制实施试验访问控制的 MLflow 托管位置 (`dbfs:/databricks/mlflow-tracking/`) 中。

某些较早的试验使用可供工作区的所有用户访问的旧存储位置 (`dbfs:/databricks/mlflow/`)。

此警告指示试验使用旧的项目存储位置。

## <a name="solution"></a>解决方案

在将项目记录到试验时，应始终使用 MLflow 托管的 DBFS 存储位置。 这样可以防止对 MLflow 项目进行意外或未经授权的访问。