---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 09/22/2020
title: 访问 MLflow 试验项目时出现 PERMISSION_DENIED 错误 - Azure Databricks
description: 解决尝试访问 MLflow 试验项目时出现的 PERMISSION_DENIED 错误。
category: Machine Learning
author: arjundc-db
db-author: arjun.dcunha@databricks.com
ms.openlocfilehash: 4a64db7d572f7b6c897440eaddfa0efdd43b2eca
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589662"
---
# <a name="permission_denied-error-when-accessing-mlflow-experiment-artifacts"></a>访问 MLflow 试验项目时出现 `PERMISSION_DENIED` 错误

## <a name="problem"></a>问题

尝试使用 MLflow 客户端访问 MLflow 项目时收到 `PERMISSION_DENIED` 错误。

```console
RestException: PERMISSION_DENIED: User <user> does not have permission to 'View' experiment with id <experiment-id>
```

或

```console
RestException: PERMISSION_DENIED: User <user> does not have permission to 'Edit' experiment with id <experiment-id>
```

## <a name="cause"></a>原因

随着针对项目的 MLflow 试验权限的扩展，必须具有显式权限才能访问 MLflow 试验的项目。

此错误表示你没有访问试验项目的权限。

## <a name="solution"></a>解决方案

让试验所有者为你授予适当的权限级别以访问试验。

[试验权限](/databricks/security/access-control/workspace-acl#mlflow-artifact-permissions)自动应用于试验项目。