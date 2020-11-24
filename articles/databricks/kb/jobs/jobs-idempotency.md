---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 如何确保作业的幂等性 - Azure Databricks
description: 了解如何确保通过 Azure Databricks REST API 提交的作业不会在请求超时后重试时重复。
ms.openlocfilehash: 782aa5964d01ea24e7f493c815b3d4c749b255af
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589972"
---
# <a name="how-to-ensure-idempotency-for-jobs"></a>如何确保作业的幂等性

通过 Azure Databricks 作业 REST API 提交作业时，不保证幂等性。 如果客户端请求超时，并且客户端重新提交了相同的请求，则最终可能会运行重复的作业。

为了确保通过作业 API 提交作业时作业的幂等性，可以使用幂等性令牌为特定的作业运行定义唯一值。 如果由于网络错误客户端没有收到响应而必须重试同一个作业，则客户端可以使用相同的幂等性令牌重试该作业，以确保不会触发重复的作业运行。

以下是使用值为 `123` 的 `idempotency_token` 的[运行提交](/databricks/dev-tools/api/latest/jobs#runs-submit) API 的 REST API 有效负载示例：

```json
{
  "run_name":"my spark task",
  "new_cluster": {
    "spark_version":"5.5.x-scala2.11",
    "node_type_id":"Standard_D3_v2",
    "num_workers":10
  },
  "libraries": [
    {
      "jar":"dbfs:/my-jar.jar"
    },
    {
      "maven": {
        "coordinates":"org.jsoup:jsoup:1.7.2"
      }
    }
  ],
  "spark_jar_task": {
    "main_class_name":"com.databricks.ComputeModels"
  },
  "idempotency_token":"123"
}
```

使用相同幂等性令牌的所有请求都应返回 200 并具有相同的运行 ID。