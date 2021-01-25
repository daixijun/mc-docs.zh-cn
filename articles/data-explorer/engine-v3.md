---
title: Azure 数据资源管理器 Kusto EngineV3（预览版）
description: 了解有关 Azure 数据资源管理器 (Kusto) EngineV3 的详细信息
author: orspod
ms.author: v-tawe
ms.reviewer: avnera
ms.service: data-explorer
ms.topic: conceptual
origin.date: 10/11/2020
ms.date: 01/22/2021
ms.openlocfilehash: 09747a9b805a792171f0d36a855bd0cf7d407088
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611698"
---
# <a name="enginev3---preview"></a>EngineV3 - 预览版

Kusto EngineV3 是 Azure 数据资源管理器的新一代存储和查询引擎。 它旨在为引入和查询遥测、日志和时序数据提供无与伦比的性能。

EngineV3 包括新的优化存储格式和索引。 EngineV3 使用高级数据统计查询优化来创建最佳查询计划和实时已编译查询执行。 EngineV3 还改进了磁盘缓存，使查询性能与当前引擎 (EngineV2) 相比提高了一个数量级。 EngineV3 为 Azure 数据资源管理器服务的下一轮创新奠定了基础。

在 EngineV3 模式下运行的 Azure 数据资源管理器群集与 EngineV2 完全兼容，因此不需要进行数据迁移。

> [!IMPORTANT]
> EngineV3 将在以下阶段推出：
>
> 1. 公共预览版（当前状态）：用户可以在 EngineV3 模式下创建新群集。 在公共预览期间，群集不受 SLA 约束，也不会针对 Azure 数据资源管理器标记计费。 基础结构成本照常计费。
> 1. 正式发布 (GA)：默认情况下，所有新群集都在 EngineV3 模式下创建。 SLA 适用于所有 EngineV3 和 EngineV2 生产群集。
> 1. 正式发布后：在 EngineV2 上运行的现有工作负载迁移到 EngineV3。 Azure 数据资源管理器标记计费将恢复。

## <a name="how-enginev3-works"></a>EngineV3 的工作原理

EngineV3 是与现有列存储 (EngineV2) 和行存储（用于流式引入）并行运行的附加列存储存储引擎。 表可以一次性合并所有三个存储中的数据，从用户角度看，这种数据的“联合”是透明的。

:::image type="content" source="media\engine-v3\engine-v3-architecture.png" alt-text="Azure 数据资源管理器/Kusto EngineV3 体系结构的图示":::

表中引入的所有数据都分区为分片，这是表的水平切片。 每个分片通常包含几百万条记录，并独立于其他分片进行编码和索引。 此功能支持引擎在引入吞吐量下实现线性缩放。

分片跨群集节点均匀分布，它们缓存在本地 SSD 上和内存中。 查询规划器和查询引擎准备并执行高度分布式的并行查询，该查询通过分片分布和缓存获益。

EngineV3 侧重于优化分布式查询的“底部”。

## <a name="performance"></a>性能

查询的性能改进和速度提高得益于引擎中的两项重大更改：

* **新的和改进的分片存储格式。** 类似于 EngineV2，该存储格式是压缩列存储，特别关注了非结构化（文本）和半结构化数据类型。 EngineV3 改进了这些不同数据类型的编码。 索引已经过重新设计以提高其粒度，允许根据索引来计算查询的部分，而不扫描数据。
* **重新设计低级别分片查询引擎。** 新的分片查询会实时编译为高效机器代码，形成快速高效的融合查询计算逻辑。 查询编译由从所有分片中收集的数据统计信息引导，并根据列编码的详情进行定制。

EngineV3 对性能的影响取决于所使用的数据集、查询模式、并发和 VM SKU。 在性能测试中，使用了 100 TB 的数据集，并探讨了不同场景，其中涉及针对结构化、非结构化和半结构化数据的分析。 测试使用了相同级别的并发和相同的硬件配置，性能平均提高了约 8 倍。 实际性能提高因查询和数据集而异。

## <a name="create-an-enginev3-cluster"></a>创建 EngineV3 群集

若要[新建 EngineV3 群集](create-cluster-database-portal.md)，请在群集创建屏幕的“基础”选项卡中选择“使用 Engine V3 预览版”复选框 ：

:::image type="content" source="media/engine-v3/create-new-cluster-v3.png" alt-text="创建群集时“使用 Engine V3 预览版”复选框的屏幕截图":::

## <a name="next-steps"></a>后续步骤

[使用 Azure 数据资源管理器引入数据](ingest-data-overview.md)
