---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 作业因群集管理器核心实例请求限制而失败 - Azure Databricks
description: 了解如何解决与 API 速率限制相关的 Azure Databricks 错误。
ms.openlocfilehash: 2210ae7b878095fd41432521ac7c7b74c8586efb
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589796"
---
# <a name="job-fails-due-to-cluster-manager-core-instance-request-limit"></a>作业因群集管理器核心实例请求限制而失败

## <a name="problem"></a>问题

Azure Databricks 笔记本或作业 API 返回以下错误：

```console
Unexpected failure while creating the cluster for the job. Cause REQUEST_LIMIT_EXCEEDED: Your request was rejected due to API rate limit. Please retry your request later, or choose a larger node type instead.
```

## <a name="cause"></a>原因

此错误指示已超过群集管理器服务核心实例[请求限制](termination-reasons.md#cluster-problem-request-limit)。

群集管理器核心实例最多可支持 1000 个请求。

## <a name="solution"></a>解决方案

联系 Azure Databricks 支持，以增加设置的核心实例限制。

Azure Databricks 可将作业限制 `maxBurstyUpsizePerOrg` 的上限增加至 2000，`upsizeTokenRefillRatePerMin` 的上限增加至 120。 当限制增加时，当前正在运行的作业将受到影响。

增加这些值可以消除限制问题，但也可能导致 CPU 利用率过高。

此问题的最佳解决方案是将群集管理器核心实例替换为可支持最大数据传输速率的较大实例。

Azure Databricks 支持可以将当前群集管理器实例类型更改为较大的实例类型。