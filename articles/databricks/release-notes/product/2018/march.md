---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 2018 年 3 月 - Azure Databricks
description: Azure Databricks 新增功能和改进的 2018 年 3 月发行说明。
ms.openlocfilehash: fefc57ca0886f825ea66221e6509885953c755e2
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329236"
---
# <a name="march-2018"></a>2018 年 3 月

发布分阶段进行。 Azure Databricks 帐户可能要等到初始发布日期后的一周内才会更新。

## <a name="command-execution-details"></a>命令执行详细信息

2018 年 3 月 27 日 - 4 月 3 日：版本 2.68

在笔记本中运行命令时，现将看到详细的进度信息。

## <a name="databricks-cli-supports---profile"></a>Databricks CLI 支持 `--profile`

2018 年 3 月 27 日 - 4 月 3 日：版本 2.68

Databricks CLI 0.6.1 支持所有位置中的 `--profile`。

请参阅 [Databricks CLI](../../../dev-tools/cli/index.md)。

## <a name="acls-enabled-by-default-for-new-premium-sku-customers"></a>默认为新的高级 SKU 客户启用 ACL

2018 年 3 月 27 日 - 4 月 3 日：版本 2.68

高级 SKU 中的所有新客户现在默认启用访问控制列表 (ACL)。 现有客户必须继续手动启用 ACL。

请参阅[启用访问控制](../../../administration-guide/access-control/index.md)。

## <a name="azure-databricks-is-now-generally-available"></a>Azure Databricks 现已推出正式版

**2018 年 3 月 22 日**

我们很高兴地宣布 Azure Databricks 现已正式发布。 在过去的几周里，我们添加了一些功能，以帮助进一步改善 Azure Databricks 体验，包括：

* [虚拟网络 (VNet) 对等互连](../../../administration-guide/cloud-configurations/azure/vnet-peering.md)，使你可以将 HDInsight 上的 [Apache Kafka](../../../spark/latest/structured-streaming/kafka.md) 与 Azure Databricks 结合使用
* [Microsoft Power BI](../../../integrations/bi/power-bi.md) 现在包含内置 Spark 连接器
* [Azure Synapse Analytics 连接器](../../../data/data-sources/azure/synapse-analytics.md)
* [Azure 事件中心（和 IoT 中心）连接器](../../../spark/latest/structured-streaming/streaming-event-hubs.md)
* [Azure 数据工厂 v2（预览版）](../../../dev-tools/data-pipelines.md)支持
* [机密管理（预览版）](../../../security/secrets/index.md#secrets-user-guide)
* 更多 [Azure 区域](https://azure.microsoft.com/global-infrastructure/services/?products=databricks)
* 改进的性能和文档

当然 Azure Databricks 会继续提供与 [Azure Blob 存储](../../../data/data-sources/azure/azure-storage.md)、[Azure Data Lake Store](../../../data/data-sources/azure/azure-datalake.md) 和 [Azure Cosmos DB](../../../data/data-sources/azure/cosmosdb-connector.md) 的轻松集成。

作为对此站点上提供的文档的补充，[docs.microsoft.com](/azure-databricks/) 提供了介绍性材料、关于 Azure 帐户管理的信息和端到端教程。

## <a name="new-doc-site-theme"></a>新文档站点主题

**2018 年 3 月 21 日**

我们已更新文档网站的外观。 希望你喜欢！

## <a name="autoscaling-local-storage"></a>自动缩放本地存储

2018 年 3 月 13 日 - 20 日：版本 2.67

Azure Databricks 上的所有群集启用时均启用了自动缩放本地存储。 这意味着，每当运行群集辅助角色节点 VM 的磁盘存储空间不足时，Azure Databricks 就会自动向这些 VM 附加其他托管磁盘。

有关详细信息，请参阅[自动缩放本地存储](../../../clusters/configure.md#autoscaling-local-storage-azure)。

## <a name="virtual-network-vnet-peering"></a>虚拟网络 (VNet) 对等互连

2018 年 3 月 13 日 - 20 日：版本 2.67

添加对虚拟网络 (VNet) 对等互连的支持，这使正在运行 Azure Databricks 资源的虚拟网络可与另一个 Azure 虚拟网络对等互连。

有关详细信息，请参阅[将虚拟网络对等互连](../../../administration-guide/cloud-configurations/azure/vnet-peering.md)。

## <a name="cluster-event-log"></a>群集事件日志

2018 年 3 月 13 日 - 20 日：版本 2.67

“群集详细信息”页具有一个新的“事件日志”选项卡，该选项卡显示重要的群集生命周期事件。 可以查看 60 天的历史事件，相当于 Azure Databricks 中的其他数据保留时间。

有关详细信息，请参阅[群集事件日志](../../../clusters/clusters-manage.md#event-log)。

## <a name="databricks-cli-060-release"></a>Databricks CLI：版本 0.6.0

2018 年3 月 13 日：databricks-cli 0.6.0

Databricks CLI 现在支持 Python 3。

有关详细信息，请参阅 [Databricks CLI](../../../dev-tools/cli/index.md)。

## <a name="job-run-management"></a>作业运行管理

2018 年 3 月 13 日 - 20 日：版本 2.67

现可在“作业详细信息”页和“作业运行”页中删除作业运行。

作业运行[获取输出](../../../dev-tools/api/latest/jobs.md#jobsjobsservicegetrunoutput)终结点已正式发布，返回的最大输出已增加到 5 MB。

## <a name="edit-cluster-permissions-now-requires-edit-mode"></a>现在需要在编辑模式下编辑群集权限

2018 年 3 月 13 日 - 20 日：版本 2.67

以前，无需单击“编辑”即可编辑群集的权限，这与其他群集属性不一致。

此更改的副作用是，在群集挂起时，无法再编辑群集权限。

## <a name="databricks-ml-model-export"></a>Databricks ML 模型导出

**2018 年 3 月 1 日**

本文档现在介绍如何使用 Databricks ML 模型导出，通过它你可以从 Apache Spark 导出模型和完整的 ML 管道。 可将这些导出的模型和管道导入到其他（Spark 和非 Spark）平台，以进行评分和预测。 模型导出面向低延迟、轻型且支持 ML 的应用程序。

> [!NOTE]
>
> 此功能需要 Databricks Runtime 4.0+。

有关详细信息，请参阅[导出和导入模型](../../../applications/machine-learning/model-export/index.md)。