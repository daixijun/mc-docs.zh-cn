---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 群集意外终止 - Azure Databricks
description: 了解如何排查 Azure Databricks 群集意外停止的问题。
ms.openlocfilehash: c538fe7c73abcb061bc05f825714a3f2b71032ad
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589721"
---
# <a name="unexpected-cluster-termination"></a><a id="cluster-unexpected-termination"> </a><a id="unexpected-cluster-termination"> </a>群集意外终止

有时，群集会意外终止，而不是[手动终止](/databricks/clusters/clusters-manage#manual-termination)或所配置的[自动终止](/databricks/clusters/clusters-manage#automatic-termination)。 群集可能由于许多原因而终止。 有些终止是由 Azure Databricks 发起的，有些终止是由云提供商发起的。 本文介绍了终止原因和修正步骤。

## <a name="azure-databricks-initiated-request-limit-exceeded"></a><a id="azure-databricks-initiated-request-limit-exceeded"> </a><a id="cluster-problem-request-limit"> </a>超出了 Azure Databricks 发起的请求限制

若要防止 API 滥用，请确保服务质量，并防止意外创建太多的大型群集。Azure Databricks 会限制所有扩大群集规模的请求，包括群集创建、启动和重设大小。  此限制使用[令牌 Bucket 算法](https://en.wikipedia.org/wiki/Token_bucket)来限制任何人能够在 Databricks 部署中按定义的时间间隔启动的节点总数，但允许特定大小的突发请求。  来自 Web UI 和 API 的请求都存在速率限制。 当群集请求超出速率限制时，超出限制的请求会失败，并出现 `REQUEST_LIMIT_EXCEEDED` 错误。

### <a name="solution"></a>解决方案

如果你达到了合法工作流的限制，Databricks 建议你执行以下操作：

* 在几分钟后重试你的请求。
* 在计划的时间范围内均匀分散你的重复执行工作流。
  例如，尝试将所有[作业](/databricks/jobs)分布在一小时内的不同间隔内，而不是笼统地将其安排在一小时边界内运行。
* 请考虑使用[节点类型](/databricks/clusters/configure#node-types)较大且节点数较少的群集。
* 使用[自动缩放](/databricks/clusters/configure#autoscaling)群集。

如果这些选项不适用于你，请联系 Azure Databricks 支持人员，请求其提高你的核心实例数限制。

对于 Azure Databricks 发起的其他终止原因，请参阅[终止代码](/databricks/dev-tools/api/latest/clusters#terminationcode)。

## <a name="cloud-provider-initiated-terminations"></a>云提供商发起的终止

本文列出了常见的与云提供商相关的终止原因和修正步骤。

### <a name="launch-failure"></a>Launch failure（启动失败）

这种终止原因出现在 Azure Databricks 无法获取虚拟机的时候。
系统会传播来自 API 的有助于排查问题的错误代码和消息。

**OperationNotAllowed**

你已经达到了配额限制，该限制通常是你的订阅可以启动的核心数。 请在 Azure 门户中请求提高限制。 请参阅 [Azure 订阅和服务限制、配额和约束](/azure-subscription-service-limits)。

**PublicIPCountLimitReached**

你已达到了你可以运行的公共 IP 的限制数。 请在 Azure 门户中请求提高限制。

**SkuNotAvailable**

你选择的资源 SKU（例如 VM 大小）不可用于你选择的位置。 若要解决此问题，请参阅[解决 SKU 不可用错误](/azure-resource-manager/resource-manager-sku-not-available-errors)。

**ReadOnlyDisabledSubscription**

你的订阅已被禁用。 请按照[为何我的 Azure 订阅被禁用？如何重新激活它？](/billing/billing-subscription-become-disable)中的步骤重新激活你的订阅。

**ResourceGroupBeingDeleted**

如果有人在 Azure 门户中取消了你的 Azure Databricks 工作区，而你此时在尝试创建群集，则可能会发生这种情况。 群集失败，因为正在删除资源组。

**SubscriptionRequestsThrottled**

你的订阅即将达到 Azure 资源管理器请求限制（请参阅[限制资源管理器请求](/azure-resource-manager/resource-manager-request-limits)）。 典型原因是 Azure Databricks 外部的另一系统正在对 Azure 进行大量的 API 调用。 请联系 Azure 支持部门来查明此系统，然后减少 API 调用数。

### <a name="communication-lost"></a>通信丢失

**Azure Databricks 能够启动群集，但失去了与承载 Spark 驱动程序的实例的连接。**

由驱动程序虚拟机关闭或网络问题导致。