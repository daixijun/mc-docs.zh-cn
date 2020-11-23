---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 通过 Airflow 运行作业时出现“访问令牌无效”错误 - Azure Databricks
description: 了解如果在通过 Airflow 使用 Azure Databricks 作业时收到“访问令牌无效”错误，该要执行哪些操作。
ms.openlocfilehash: 2082ebbc76ee11ae188fb5da78bd7dec8f8bb34b
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589600"
---
# <a name="invalid-access-token-error-when-running-jobs-with-airflow"></a>通过 Airflow 运行作业时出现 `Invalid Access Token` 错误

## <a name="problem"></a>问题

运行计划的 Airflow Azure Databricks 作业时，出现以下错误：

```console
Invalid Access Token : 403 Forbidden Error
```

## <a name="cause"></a>原因

若要通过 Airflow 运行或计划 Azure Databricks 作业，需要使用 Airflow Web UI 配置 Azure Databricks 连接。 以下任何错误设置都可能导致错误：

* 将 `host` 字段设置为 Azure Databricks 工作区主机名。
* 将 `login` 字段设置为 `token`。
* 将 `password` 字段设置为 Azure Databricks 生成的个人访问令牌。
* 将 `Extra` 字段设置为 JSON 字符串，其中的密钥是 `token` ，值是你的个人访问令牌。

Azure Databricks 生成的个人访问令牌的有效期通常为 90 天。 如果令牌过期，则会发生此 `403 Forbidden Error`。

## <a name="solution"></a>解决方案

* 验证 `Extra` 字段是否已使用 JSON 字符串正确配置：

  ```json
  {"token": "<your personal access token>"}
  ```

* 验证 `password` 字段和 `Extra` 字段中是否都提及了该令牌。
* 验证 `host`、`login` 和 `password` 字段是否均已正确配置。
* 验证个人访问令牌是否未过期。

如有必要，可[生成新令牌](/databricks/dev-tools/api/latest/authentication)。