---
title: 如何在 Azure Cosmos DB 上的预配吞吐量与无服务器之间进行选择
description: 了解如何针对你的工作负荷在预配吞吐量与无服务器之间进行选择。
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 11/25/2020
author: rockboyfor
ms.date: 01/18/2021
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.openlocfilehash: e4e2a4994ef85a9bdf02cfa69d078283648501e7
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98230168"
---
<!--Verified Successfully-->
# <a name="how-to-choose-between-provisioned-throughput-and-serverless"></a>如何在预配吞吐量与无服务器之间进行选择
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 提供了两种不同的容量模式：[预配吞吐量](set-throughput.md)和[无服务器](serverless.md)。 在这两种模式下，你可以执行完全相同的数据库操作，但这些操作的收费方式截然不同。

<!--MOONCAKE CUSTOMIZATION REMOVE: The following video explains the core differences between these modes and how they fit different types of workloads:-->
<!--Not Available on [!VIDEO https://www.youtube.com/embed/CgYQo6uHyt0]-->

## <a name="detailed-comparison"></a>详细比较

| 条件 | 预配的吞吐量 | 无服务器 |
| --- | --- | --- |
| 状态 | 正式发布 | 预览 |
| 最适用于 | 具有持续流量的工作负载，需要可预测的性能 | 具有间歇性或不可预测流量且平均峰值流量比低的工作负载 |
| 工作原理 | 对于每个容器，你预配一定数量的吞吐量（以每秒[请求单位数](request-units.md)表示）。 每秒此数量的请求单位可用于你的数据库操作。 预配的吞吐量可以手动更新，也可以通过[自动缩放](provision-throughput-autoscale.md)自动调整。 | 你对容器运行数据库操作，无需预配任何容量。 |
| 地理分布 | 可用（Azure 区域数不受限制） | 不可用（无服务器帐户只能在 1 个 Azure 区域中运行） |
| 每个容器的最大存储 | 无限制 | 50 GB |
| 性能 | SLA 涵盖的针对点读取和写入的延迟 < 10 毫秒 | SLA 涵盖的针对点读取的延迟 < 10 毫秒，针对写入的延迟 < 30 毫秒 |
| 计费模式 | 对于预配的 RU/s，无论消耗了多少 RU，都按每小时计费。 | 根据数据库操作所消耗的 RU 数量，按小时收费。 |

> [!IMPORTANT]
> 当无服务器正式发布时，一些无服务器限制可能会被解除或删除，**你的反馈** 将有助于我们做出决定！ 请与我们联系，并告诉我们有关无服务器体验的详细信息：[azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com)。

## <a name="estimating-your-expected-consumption"></a>估计预期消耗量

在某些情况下，可能无法确定应当为给定的工作负荷选择预配吞吐量还是选择无服务器。 为了帮助做出此决定，你可以估算总体“预期消耗”，即一个月内可能消耗的 RU 总数。

<!--Not Available on [here](plan-manage-costs.md#estimating-serverless-costs)-->

<!--Not Available on price unit-->

## <a name="next-steps"></a>后续步骤

- 详细了解如何[在 Azure Cosmos DB 上预配吞吐量](set-throughput.md)
- 详细了解 [Azure Cosmos DB 无服务器](serverless.md)
- 熟悉[请求单位](request-units.md)的概念

<!-- Update_Description: update meta properties, wording update, update link -->