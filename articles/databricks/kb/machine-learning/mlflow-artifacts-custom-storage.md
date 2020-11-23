---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 09/22/2020
title: 使用自定义项目存储位置时出现试验警告 - Azure Databricks
description: 解决问题：在使用自定义项目存储位置而不是 MLflow 托管位置时出现试验警告。
category: Machine Learning
author: arjundc-db
db-author: arjun.dcunha@databricks.com
ms.openlocfilehash: 948ca151405e3cc3455c8f537c559b9636a66afb
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589720"
---
# <a name="experiment-warning-when-custom-artifact-storage-location-is-used"></a>使用自定义项目存储位置时出现试验警告

## <a name="problem"></a>问题

使用自定义项目位置创建 MLflow 试验时，会收到以下警告：

> [!div class="mx-imgBorder"]
> ![no-alternative-text](../_static/images/machine-learning/custom-artifact-location-warning.png)

## <a name="cause"></a>原因

现在，会对 MLflow 跟踪中的项目强制执行 [MLflow 试验权限](/databricks/security/access-control/workspace-acl#mlflow-artifact-permissions)，这使你可以轻松控制对数据集、模型和其他文件的访问。

MLflow 无法保证对自定义位置中存储的项目强制执行访问控制。

## <a name="solution"></a>解决方案

Databricks 建议在创建 MLflow 试验时使用默认项目位置。

默认存储位置由访问控制支持。