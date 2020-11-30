---
title: include 文件
description: include 文件
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 11/17/2020
author: deepakpalled
ms.author: v-junlch
manager: diviso
ms.custom: include file
ms.openlocfilehash: a5ebda6f31bcda871b28b0b606b7bd17bd4f0fc0
ms.sourcegitcommit: b072689d006cbf9795612acf68e2c4fee0eccfbc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94885446"
---
### <a name="property-limits"></a>属性限制

对于暖存储，Azure 时序见解属性限制已增加到 1,000，而对于冷存储，则没有属性限制。 提供的事件属性具有相应的 JSON、CSV 和图表列，可以在 Azure 时序见解第 2 代资源管理器中查看它们。

| SKU | 最大属性数 |
| --- | --- |
| 第 2 代 (L1) | 对于暖存储，限制为 1,000 个属性（列），而对于冷存储，则无限制。|
| 第 1 代 (S1) | 600 属性（列） |
| 第 1 代 (S2) | 800 属性（列） |

### <a name="streaming-ingestion"></a>流式引入

* 每个环境最多只能有两个[事件源](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md)。

* 有关事件源的最佳做法和一般指南，可在[此处](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)找到

* 默认情况下，对于每个 Azure 时序见解第 2 代环境，Azure 时序见解第 2 代可按 **每秒最多 1 兆字节 (MBps)** 的速率引入传入的数据。 存在针对[单个中心分区](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits)的其他限制。 通过 Azure 门户提交支持票证最多可获得 8 MBps 的速率。 若要了解详细信息，请参阅[流式引入吞吐量限制](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md)。

### <a name="api-limits"></a>API 限制

[REST API 参考文档](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1)中指定了针对 Azure 时序见解第 2 代的 REST API 限制。

