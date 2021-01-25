---
title: Kusto 引入客户端库最佳做法 - Azure 数据资源管理器
description: 本文介绍了有关 Kusto 引入客户端库的最佳做法。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 08/16/2019
ms.date: 01/22/2021
ms.openlocfilehash: b6219bb441fa0fcacc9b62588126dd3acc2be5d6
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611702"
---
# <a name="kusto-ingest-client-library---best-practices"></a>Kusto 引入客户端库 - 最佳做法

## <a name="select-the-right-ingestclient-flavor"></a>选择正确的 IngestClient 风格

请使用 [KustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)，这是建议的本机数据引入模式。 原因如下：
* 在引擎停机期间（例如在部署期间），无法进行直接引入。 在排队引入模式下，请求将保留到 Azure 队列中，并且数据管理服务将根据需要重试。
* 数据管理服务使引擎不会发生引入请求过载。 使用“直接引入”重写此控制可能会严重影响引擎引入和查询性能。
* 数据管理将聚合多个引入请求。 聚合会优化要创建的初始分片（区）的大小。
* 获取有关每个引入的反馈非常简单。

## <a name="avoid-tracking-ingest-operation-status"></a>避免跟踪引入操作状态

[跟踪引入操作状态](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient)很有用。 但对于大容量数据流，应避免为每个引入请求启用用于指示该请求存在的通知。 这样的跟踪会对基础 xStore 资源施加非常大的负担，从而可能导致引入延迟，甚至群集完全无响应。

## <a name="optimizing-for-throughput"></a>针对吞吐量进行优化

如果以大区块的方式执行引入，引入效果最佳。 
* 它消耗的资源最少
* 它生成在最大程度上进行了 COGS（所售货物成本）优化的数据分片，并产生最佳数据事务

我们建议，使用 Kusto.Ingest 库引入数据或直接将数据引入到引擎的客户按每批 100 MB 到 1 GB（未压缩）的数据量分批发送数据
* 当直接使用引擎时，上限对帮助减少引入过程使用的内存量非常重要 

> [!NOTE]
> 使用 `KustoQueuedIngestClient` 类时，过大的数据块会拆分为较小的区块，并且在到达引擎之前，这些小区块将在一定程度上聚合。

* 引入数据大小的下限也很重要，尽管重要程度并非最高。 每隔一段时间就小批引入数据非常适用，尽管效率略低于使用大批。 如果客户需要引入大量数据，并且在将数据发送到引擎之前，不能将它们分批转换为大型区块，则 `KustoQueuedIngestClient` 类也可以解决问题。

## <a name="other-factors-that-impact-ingestion-throughput"></a>影响引入吞吐量的其他因素

多个因素可能会影响引入吞吐量。 在规划引入管道时，请确保评估以下要点，这些要点可能会对 COG 产生重要影响。

| 要考虑的因素 |  说明                                                                                              |
|--------------------------|-----------------------------------------------------------------------------------------------------------|
| 数据格式              | CSV 是最快的引入格式。 对于同一数据量，JSON 需要花费的时间通常会多 2 或 3 倍。|
| 表宽度              | 请确保仅引入你真正需要的数据。 表越宽，需要编码和索引编制的列就越多，吞吐量也越低。 可以通过提供引入映射来控制要引入哪些字段。       |
| 源数据位置     | 避免跨区域读取可加快引入速度。                                                       |
| 群集上的负载      | 当群集遇到较高的查询负载时，引入将需要更长的时间才能完成，从而会减少吞吐量。|

## <a name="optimizing-for-cogs"></a>针对 COGS 进行优化

使用 Kusto 客户端库将数据引入 Azure 数据资源管理器仍是最便宜且最可靠的选项。 我们强烈建议客户查看其引入方法，以针对 COGS（所售货物成本）进行优化，并利用 Azure 存储定价，该定价将使 blob 事务明显经济高效。

* **限制引入数据区块的数量**。
    为了更好地控制 Azure 数据资源管理器引入成本并减少每月费用，请限制引入数据区块（文件/blob/流）的数量。
* **引入较大的数据区块（最多 1GB 未压缩的数据）** 。 
    许多团队尝试引入数千万的小数据区块来实现较低的延迟，这非常低效且成本高昂。 
* **批处理**。 客户端的任意量批处理都将提高优化程度。 
* **为 Kusto.Ingest 客户端提供确切的未压缩数据大小**。
    如果不这样做，可能会导致额外的存储事务。
* **避免在将 `FlushImmediately` 标志设置为 `true` 时发送要引入的数据**。 另外，请避免发送设置了 `ingest-by`/`drop-by` 标记的小区块。 如果使用这些方法，这些方法将：
     * 阻止服务在引入过程中正确聚合数据
     * 在引入后导致不必要的存储事务
     * 影响 COGS 
     * 可能会导致群集的引入或查询性能下降（如果过度使用）
