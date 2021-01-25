---
title: 缓存策略（热缓存和冷缓存）- Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的缓存策略（热缓存和冷缓存）。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/19/2020
ms.date: 01/22/2021
ms.openlocfilehash: 079c1839ab24cccc6899ca055792494ecb9928f1
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611460"
---
# <a name="cache-policy-hot-and-cold-cache"></a>缓存策略（热缓存和冷缓存） 

Azure 数据资源管理器会将其引入数据存储在可靠的存储中（通常是 Azure Blob 存储），使这些数据远离其实际处理（例如 Azure 计算）节点。 为了加快对数据的查询，Azure 数据资源管理器会将这些数据或部分数据缓存在其处理节点 SSD 甚至 RAM 中。 Azure 数据资源管理器包含一种复杂的缓存机制，旨在智能地确定要缓存的数据对象。 缓存使 Azure 数据资源管理器能够描述其使用的数据项目，这样一来，使更重要的数据可以占据优先地位。 例如，列索引和列数据分片。

当所有引入数据缓存后，可以获得最佳查询性能。 有时，对于某些数据而言，在本地 SSD 存储中“暖”存储这些数据所花费的成本是不值得的。
例如，许多团队认为很少访问的早期日志记录并非那么重要。
他们愿意在查询这些数据时拥有降低的性能，而不是花钱让数据一直保持为暖状态。

Azure 数据资源管理器缓存提供了一种精细的缓存策略，客户可以使用此策略来区分热数据缓存和冷数据缓存  。 Azure 数据资源管理器缓存尝试将属于热数据缓存类别的所有数据保留在本地 SSD（或 RAM）中，直到达到定义的热数据缓存大小为止。 剩余的本地 SSD 空间将用于保存未分类为“热”的数据。 这种设计的一个有用之处在于，从可靠存储中加载大量冷数据的查询不会将数据从热数据缓存中逐出。 因此，不会对涉及热数据缓存中的数据的查询产生重大影响。

设置热缓存策略的主要影响在于：
* **成本**：使可靠存储的成本显著低于本地 SSD。 目前，在 Azure 中，它的成本要便宜近 45 倍。
* **性能**：当数据存储在本地 SSD 中时，查询数据的速度更快，特别对于扫描大量数据的范围查询而言。  

使用[缓存策略命令](cache-policy.md)来管理缓存策略。

> [!TIP]
>Azure 数据资源管理器专为临时查询而设计，中间结果集适合群集的总 RAM。
>对于需要将中间结果存储在持久性存储（例如 SSD）中的大型作业（例如 map-reduce），请使用连续导出功能。 此功能使你可以使用 HDInsight 或 Azure Databricks 等服务执行长时间运行的批处理查询。
 
## <a name="how-cache-policy-is-applied"></a>如何应用缓存策略

将数据引入 Azure 数据资源管理器时，系统将跟踪引入的日期和时间以及创建的盘区。 盘区的引入日期和时间值（如果盘区是从多个预先存在的盘区中构建的，则为最大值）用于计算缓存策略。

> [!NOTE]
> 可以使用引入属性 `creationTime` 来指定引入日期和时间的值。
> 这样做时，请确保表的有效[盘区合并策略](mergepolicy.md)中的 `Lookback` 属性与你为 `creationTime` 设置的值一致。

默认情况下，有效策略为 `null`，这意味着所有数据都被视为热数据。
非 `null` 表级别的策略会重写数据库级别的策略。

## <a name="scoping-queries-to-hot-cache"></a>将查询范围限定为热缓存

Kusto 支持范围仅限于热缓存数据的查询。

> [!NOTE]
> 数据范围设置仅适用于支持缓存策略的实体，例如表。 对于其他实体（例如外部表），它会被忽略。

以下为几种可能的查询：
* 将名为 `query_datascope` 的客户端请求属性添加到查询中。
   可能的值：`default`、`all` 和 `hotcache`。
* 在查询文本中使用 `set` 语句：`set query_datascope='...'`。
   可能的值与客户端请求属性的值相同。
* 在查询正文中紧跟表引用的后面添加 `datascope=...` 文本。 
   可能的值为 `all` 和 `hotcache`。

`default` 值指示使用群集默认设置，该设置决定查询应涵盖所有数据。

如果不同的方法之间存在差异，`set` 则优先于客户端请求属性。 为表引用指定值要优先于两者。

例如，在下面的查询中，所有表引用将仅使用热缓存数据，除了对“T”的第二个引用，该引用的范围是所有数据：

```kusto
set query_datascope="hotcache";
T | union U | join (T datascope=all | where Timestamp < ago(365d) on X
```

## <a name="cache-policy-vs-retention-policy"></a>缓存策略与保留策略

缓存策略独立于[保留策略](./retentionpolicy.md)： 
- 缓存策略定义如何设置资源的优先级。 查询重要数据的速度将更快，并且能够抵抗查询不太重要的数据所造成的影响。
- 保留策略定义表/数据库中可查询数据的范围（特别是 `SoftDeletePeriod`）。

根据预期的查询模式，配置此策略以在成本和性能之间实现最佳平衡。

示例：
* `SoftDeletePeriod` = 56d
* `hot cache policy` = 28d

在此示例中，最后 28 天的数据将位于群集 SSD 上，另外 28 天的数据将存储在 Azure Blob 存储中。
你可以对全部 56 天的数据运行查询。
 
