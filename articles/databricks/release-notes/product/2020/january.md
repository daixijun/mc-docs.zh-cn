---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/04/2020
title: 2020 年 1 月 - Azure Databricks
description: 新 Azure Databricks 功能和改进的 2020 年 1 月发行说明。
ms.openlocfilehash: ccb84875e0421c0381e34a463ec4041abfa04a17
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329161"
---
# <a name="january-2020"></a>2020 年 1 月

这些功能和 Azure Databricks 平台的改进已于 2020 年 1 月发布。

> [!NOTE]
>
> 发布分阶段进行。 在初始发布日期后，可能最长需要等待一周，你的 Azure Databricks 帐户才会更新。
>
> 本月发布了 Azure Databricks 平台版本 3.9 和 3.11。 没有发布版本 3.10 或 3.8。 版本 3.7 是只与稳定性和修补程序有关的版本。

## <a name="coming-soon-workspace-pool-and-cluster-tags-propagate-to-dbu-usage-details-and-azure-vms-for-better-cost-management-reporting"></a>即将推出：工作区、池和群集标记将传播到 DBU 使用情况详细信息和 Azure VM，以便改进成本管理报告

2 月 10 日，我们将发布 Azure Databricks 使用情况详细信息和 Azure VM 的标记传播。 新的标记传播功能将 Azure Databricks 工作区标记（即资源组标记）、池标记和群集标记合并，并将它们传播到 Databricks DBU 使用情况详细信息和 Azure VM 作为资源标记。 你将能够在 Azure 成本管理门户和使用情况详细信息导出内容中查看组合标记信息，从而更好地了解 Azure Databricks 使用情况（总拥有成本）以及各业务部门和团队的使用情况。

## <a name="azure-databricks-and-azure-lighthouse-can-now-live-in-the-same-subscription"></a>Azure Databricks 和 Azure Lighthouse 现在可以在同一订阅中共存

2020 年 1 月 29 日

所有现有的 Azure Databricks 工作区都从使用托管锁迁移到[拒绝分配](/role-based-access-control/deny-assignments)。 创建的所有新工作区都将具有“拒绝分配”。 这不会改变任何现有行为，并且安全级别保持不变。 尽管你可以加入使用 Azure Databricks 的订阅，但管理租户中的用户目前无法在委托订阅上启动 Azure Databricks 工作区。

## <a name="databricks-runtime-63-for-genomics-ga"></a>用于基因组学的 Databricks Runtime 6.3 正式版

**2020 年 1 月 22 日**

用于基因组学的 Databricks Runtime 6.3 是基于 Databricks Runtime 6.3 构建的。 它包含用于基因组学的 Databricks Runtime 6.2 的许多改进和升级。

关键功能包括：

* 支持 Delta 表充当联合基因分型管道的输入
* 读取 VCF 时自动进行批注分析
* 改进了多等位基因变体拆分器
* 提升了线性和逻辑回归函数的效率

有关详细信息，请参阅完整的[用于基因组学的 Databricks Runtime 6.3（不支持）](../../runtime/6.3genomics.md)发行说明。

## <a name="databricks-runtime-63-ml-ga"></a>Databricks Runtime 6.3 ML 正式版

**2020 年 1 月 22 日**

Databricks Runtime 6.3 ML 的正式发布引入了许多库升级，其中包括：

* PyTorch：1.3.0 到 1.3.1
* torchvision：0.4.1 到 0.4.2
* MLflow：1.4.0 到 1.5.0
* Hyperopt：0.2.1 到 0.2.2

有关详细信息，请参阅完整的 [Databricks Runtime 6.3 ML（不支持）](../../runtime/6.3ml.md)发行说明。

## <a name="databricks-runtime-63-ga"></a>Databricks Runtime 6.3 正式版

**2020 年 1 月 22 日**

Databricks Runtime 6.3 的正式发布引入了新功能、改进和许多 bug 修补程序。

此版本引入了增强的并发性。 关键功能包括：

* 改进了所有 Delta Lake 操作的并发
* 改进了文件压缩支持
* 改进了仅插入合并的性能

有关详细信息，请参阅完整的 [Databricks Runtime 6.3（不支持）](../../runtime/6.3.md)发行说明。

## <a name="delta-cache-enabled-by-default"></a>默认情况下已启用增量缓存

2020 年 1 月 7-14 日：版本 3.9

对于所有受支持的 Databricks Runtime 版本，Lsv2 系列实例上默认启用 Delta 缓存。 请参阅[使用增量缓存](../../../delta/optimizations/delta-cache.md#worker-instance-type)。

## <a name="cluster-standard-autoscaling-step-is-now-configurable"></a>群集标准自动缩放步骤现在可配置

2020 年 1 月 7-14 日：版本 3.9

默认情况下，标准自动缩放的第一步会添加 8 个节点。 现在，你可以在群集 Spark 配置中设置步骤值。 请参阅[标准自动缩放](../../../clusters/configure.md#standard-autoscaling)。

## <a name="scim-api-supports-pagination-for-get-users-and-get-groups-public-preview"></a>SCIM API 对“获取用户”和“获取组”支持分页（公共预览版）

2020 年 1 月 7-14 日：版本 3.9

SCIM API 现在支持“获取用户”和“获取组”的分页。 指定 `startIndex` 和 `count` 查询参数时，SCIM 将返回部分用户/组。 `startIndex` 参数是第一个结果的从 1 开始的索引。 `count` 参数是要返回的用户或组的最大数量。 这可确保 SCIM 客户端的可缩放性，并简化 Azure Databricks 管理员的 SCIM 调用。 请参阅 [SCIM API](../../../dev-tools/api/latest/scim/index.md)。

## <a name="file-browser-swimlane-widths-increased-to-240px"></a>文件浏览器泳道宽度增加到 240px

2020 年 1 月 7-14 日：版本 3.9

增加的宽度减少了将鼠标悬停在对象上以查看完整文件名的需要。

## <a name="databricks-runtime-35-lts-support-ends"></a>Databricks Runtime 3.5 LTS 支持结束

2020 年 1 月 2 日

对 Databricks Runtime 3.5 LTS（长期支持）的支持在 1 月 2 日结束。 请参阅 [Databricks 运行时支持生命周期](../../runtime/databricks-runtime-ver.md#runtime-support)。