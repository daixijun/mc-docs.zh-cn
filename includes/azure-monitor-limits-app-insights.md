---
title: include 文件
description: include 文件
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 01/15/2021
ms.author: v-johya
ms.custom: include file
ms.openlocfilehash: 8f670bfc8a11aac42e8098dcfc5a1be2e9badfaa
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98231190"
---
每个应用程序（即每个检测密钥）的指标和事件数都有一些限制。 限制取决于选择的[定价计划](https://www.azure.cn/pricing/details/application-insights/)。

| 资源 | 默认限制 | 注意
| --- | --- | --- |
| 每日的总数据量 | 100 GB | 可以通过设置一个上限来减少数据。 如果需要更多数据，可以在门户中最多将上限提高到 1,000 GB。 如需大于 1,000 GB 的容量，请将电子邮件发送到 AIDataCap@microsoft.com。
| 限制 | 32,000 事件/秒 | 限制按分钟计量。
| 数据保留 | 90 天 | 此资源适用于[搜索](../articles/azure-monitor/app/diagnostic-search.md)、[分析](../articles/azure-monitor/log-query/log-query-overview.md)和[指标资源管理器](../articles/azure-monitor/platform/metrics-charts.md)。
| [可用性多步骤测试](../articles/azure-monitor/app/availability-multistep.md)详细结果保留 | 90 天 | 此资源提供了每个步骤的详细结果。
| 最大事件大小 | 64,000 |
| 属性和指标名称长度 | 150 | 请参阅[类型架构](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/)。
| 属性值字符串长度 | 8,192 | 请参阅[类型架构](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/)。
| 跟踪和异常消息长度 | 32,768  | 请参阅[类型架构](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/)。
| 每个应用的可用性测试计数 | 100 |

有关详细信息，请参阅[关于 Application Insights 中的定价和配额](../articles/azure-monitor/app/pricing.md)。
