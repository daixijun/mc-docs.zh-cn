---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 作业因作业速率限制而失败 - Azure Databricks
description: 了解如何解决因作业速率限制而导致的 Azure Databricks 作业失败问题。
ms.openlocfilehash: c71f64d2b58c4d73c734c5035edcacc79c0865d1
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589865"
---
# <a name="job-fails-due-to-job-rate-limit"></a>作业因作业速率限制而失败

## <a name="problem"></a>问题

Azure Databricks 笔记本或作业 API 请求返回以下错误：

```console
Error : {"error_code":"INVALID_STATE","message":"There were already 1000 jobs created in past 3600 seconds, exceeding rate limit: 1000 job creations per 3600 seconds."}
```

## <a name="cause"></a>原因

发生此错误的原因如下：每小时的作业数超过了 Azure Databricks 为防止 API 滥用并确保服务质量而设定的上限 1000。

## <a name="solution"></a>解决方案

如果无法确保工作区中每小时创建的作业数少于 1000，请联系 Azure Databricks 支持人员，请求提高上限。 提高作业速率限制需至少停机 20 分钟。

* Azure Databricks 可将作业限制 `maximumJobCreationRate` 提高到 2000。
* 提高上限时，当前正在运行的作业将受到影响。