---
title: Azure 数据资源管理器和业务连续性灾难恢复
description: 本文介绍了可帮助从中断事件中恢复的 Azure 数据资源管理器功能。
author: orspod
ms.author: v-tawe
ms.reviewer: ankhanol
ms.service: data-explorer
ms.topic: conceptual
origin.date: 08/05/2020
ms.date: 01/22/2021
ms.openlocfilehash: d43d7957f9ee536b6f053eb4baa78ce1540c05d7
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611708"
---
# <a name="business-continuity-and-disaster-recovery-overview"></a>业务连续性和灾难恢复概述

使用 Azure 数据资源管理器中的业务连续性和灾难恢复，你的业务能够在发生中断时继续正常运转。 本文讨论可用性（区域内）和灾难恢复。 详细介绍了弹性 Azure 数据资源管理器部署的本机功能和体系结构注意事项。 并详细说明了如何在发生人为错误时进行恢复，介绍了高可用性，以及多个灾难恢复配置。 这些配置取决于恢复点目标 (RPO) 和恢复时间目标 (RTO) 等复原要求、所需的工作量和成本。

## <a name="mitigate-disruptive-events"></a>缓解中断性事件

* [人为错误](#human-error)
* [Azure 数据资源管理器的高可用性](#high-availability-of-azure-data-explorer)
* [Azure 可用性区域中断](#outage-of-an-azure-availability-zone)
* [Azure 数据中心中断](#outage-of-an-azure-datacenter)
* [Azure 区域中断](#outage-of-an-azure-region)

### <a name="human-error"></a>人为错误 

人为错误是不可避免的。 用户可能会意外删除群集、数据库或表。  

#### <a name="accidental-cluster-or-database-deletion"></a>意外删除群集或数据库

意外删除群集或数据库是无法恢复的操作。 作为 Azure 数据资源管理器资源所有者，你可以通过启用在 Azure 资源级别提供的删除[锁](/azure-resource-manager/management/lock-resources)功能来防止数据丢失。

#### <a name="accidental-table-deletion"></a>意外的表删除

允许具有表管理权限或更高权限的用户[删除表](kusto/management/drop-table-command.md)。 如果其中一个用户意外地删除了某个表，你可以使用 [`.undo drop table`](kusto/management/undo-drop-table-command.md) 命令恢复该表。 若要成功执行此命令，必须首先启用[保留策略](kusto/management/retentionpolicy.md)中的“可恢复性”属性。

#### <a name="accidental-external-table-deletion"></a>意外删除外部表

[外部表](kusto/query/schema-entities/externaltables.md)是引用存储在数据库外部的数据的 Kusto 查询架构实体。 删除外部表只会删除表元数据。 可以通过重新执行表创建命令来恢复它。 使用[软删除](/storage/blobs/storage-blob-soft-delete)功能，在用户配置的时间内防止意外删除或覆盖文件/blob。

### <a name="high-availability-of-azure-data-explorer"></a>Azure 数据资源管理器的高可用性

高可用性是指 Azure 数据资源管理器、其组件和 Azure 区域内的基本依赖项的容错能力。 这种容错避免了实现中的单一故障点 (SPOF)。 在 Azure 数据资源管理器中，高可用性包括持久性层、计算层和先导-后继配置。

#### <a name="persistence-layer"></a>持久性层

Azure 数据资源管理器利用 Azure 存储作为其持久性层。 Azure 存储自动提供容错功能，默认设置是在数据中心内提供本地冗余存储 (LRS)。 保留三个副本。 如果一个副本在使用过程中丢失，则在不中断的情况下部署另一个副本。 通过区域冗余存储，可以进一步提高复原能力，该存储可以智能地跨 Azure 区域可用性区域放置副本，在增加一定地额外成本后可实现最大容错能力。

#### <a name="compute-layer"></a>计算层

Azure 数据资源管理器是一种分布式计算平台，根据规模和节点角色类型可以有两到多个节点。 在预配时，选择可用性区域以跨区域分布节点部署，以实现最大的区域内恢复能力。 可用性区域故障不会导致完全中断，而是性能下降，直到该区域恢复为止。 

#### <a name="leader-follower-cluster-configuration"></a>先导-后继群集配置

Azure 数据资源管理器针对后面跟有其他后继群集的先导群集提供了一个可选的[后继功能](follower.md)，用于提供对先导群集的数据和元数据的只读访问权限。 先导群集中的更改，例如 `create`、`append` 和 `drop` 将自动与后继群集同步。 先导群集可以跨越 Azure 区域，而后继群集应与先导群集位于同一个区域中。 如果先导群集故障或数据库或表意外丢失，后继群集会失去访问权限，直到在先导群集中恢复访问。 

### <a name="outage-of-an-azure-availability-zone"></a>Azure 可用性区域中断

Azure 可用性区域是同一 Azure 区域中独特的物理位置。 它们可以保护 Azure 数据资源管理器群集的计算和数据不会在部分区域发生故障。 区域故障是一种与可用性相关的情况，因为它发生在区域内。 

将 Azure 数据资源管理器群集与其他已连接的 Azure 资源固定到同一区域。 有关启用可用性区域的更多信息，请参阅[创建群集](create-cluster-database-portal.md#create-a-cluster)。

> [!NOTE] 
> 只有在创建群集时才支持选择可用性区域，选择后不能修改。

### <a name="outage-of-an-azure-datacenter"></a>Azure 数据中心中断

Azure 可用性区域是有成本的，一些客户会选择在没有区域冗余的情况下进行部署。 如果采用这种 Azure 数据资源管理器部署，那么当 Azure 数据中心发生中断时，会导致群集中断。 因此，处理 Azure 数据中心中断与处理 Azure 区域中断是相同的。

### <a name="outage-of-an-azure-region"></a>Azure 区域中断

Azure 数据资源管理器不提供针对整个 Azure 区域中断的自动保护。 若要在发生此类中断时最大程度地降低业务影响，应跨 [Azure 配对区域](/best-practices-availability-paired-regions)设置多个 Azure 数据资源管理器群集。 根据恢复时间目标 (RTO)、恢复点目标 (RPO) 以及工作量和成本，有[多种灾难恢复配置](#disaster-recovery-configurations)。 可通过 Azure 顾问建议和[自动缩放](manage-cluster-horizontal-scaling.md)配置来实现成本和性能优化。

## <a name="disaster-recovery-configurations"></a>灾难恢复配置

 本节详细介绍了多种灾难恢复配置，具体取决于复原要求（RPO 和 RTO）、所需的工作量和成本。

恢复时间目标 (RTO) 是指发生中断后恢复所用的时间。 例如，RTO 为 2 小时意味着应用程序必须在中断后两小时内恢复正常运行。 恢复点目标 (RPO) 是指在发生中断后，在中断期间丢失的数据量超过允许的阈值之前可经过的时间间隔。 例如，如果 RPO 是 24 小时，而应用程序的数据是从 15 年前开始的，则它们仍处于商定的 RPO 参数范围内。

在规划灾难恢复时，引入、处理和特选过程需要预先进行精心设计。 引入是指从各种来源将数据集成到 Azure 数据资源管理器中；处理是指转换及类似活动；特选是指具体化视图、导出到数据湖等等。

以下是常用的灾难恢复配置，下面将详细介绍每种配置。
* [主动-主动-主动 (always-on) 配置](#active-active-active-configuration)
* [主动-主动配置](#active-active-configuration)
* [主动-热备用服务器配置](#active-hot-standby-configuration)
* [按需数据恢复群集配置](#on-demand-data-recovery-configuration)

### <a name="active-active-active-configuration"></a>主动-主动-主动配置

此配置也称为“always-on”。 对于不允许中断的关键应用程序部署，应该跨 Azure 配对区域使用多个 Azure 数据资源管理器群集。 在所有群集中并行设置引入、处理和特选。 不同区域的群集 SKU 必须相同。 Azure 将确保在所有 Azure 配对区域中根据具体需要和安排推出更新。 Azure 区域中断不会导致应用程序中断。 你可能会遇到延迟或性能下降的情况。

:::image type="content" source="media/business-continuity-overview/active-active-active-n.png" alt-text="主动-主动-主动-n 配置":::

| **配置** | **RPO** | **RTO** | **工作量** | **成本** |
| --- | --- | --- | --- | --- |
| **主动-主动-主动-n** | 0 小时 | 0 小时 | 较低 | 最高 |

### <a name="active-active-configuration"></a>主动-主动配置

此配置与[主动-主动-主动配置](#active-active-active-configuration)相同，但只涉及了两个 Azure 配对区域。 配置双重引入、处理和特选。 将用户路由到最近的区域。 不同区域的群集 SKU 必须相同。

:::image type="content" source="media/business-continuity-overview/active-active.png" alt-text="主动-主动配置":::

| **配置** | **RPO** | **RTO** | **工作量** | **成本** |
| --- | --- | --- | --- | --- |
| **主动-主动** | 无 | 无 | 较低 | 高 |

### <a name="active-hot-standby-configuration"></a>主动-热备用服务器配置

主动-热配置与[主动-主动配置](#active-active-configuration)在双重引入、处理和特选方面相似。 但是，备用群集对最终用户是离线状态，且不需要与主群集位于同一 SKU 中。 热备用群集也可以具有较小的 SKU 和规模，这样性能会降低。 在发生灾难的情况下，备用群集变为联机状态并进行纵向扩展。

:::image type="content" source="media/business-continuity-overview/active-hot-standby.png" alt-text="主动-热备用服务器配置":::

| **配置** | **RPO** | **RTO** | **工作量** | **成本** |
| --- | --- | --- | --- | --- |
| **主动-热备用服务器** | 低 | 低 | 中 | 中 |

### <a name="on-demand-data-recovery-configuration"></a>按需数据恢复配置

此解决方案的复原能力（最高的 RPO 和 RTO）最低、成本最低、工作量最大。 在此配置中，没有数据恢复群集。 配置特选数据（除非还需要原始数据和中间数据）的连续导出，将其导出到配置了 GRS（异地冗余存储）的存储帐户。 如果需要进行灾难恢复，会启动数据恢复群集。 此时将应用 DDL、配置、策略和流程。

:::image type="content" source="media/business-continuity-overview/on-demand-data-recovery-cluster.png" alt-text="按需数据恢复群集配置":::

| **配置** | **RPO** | **RTO** | **工作量** | **成本** |
| --- | --- | --- | --- | --- |
| **按需数据恢复群集** | 最高 | 最高 | 最高 | 最低 |

### <a name="summary-of-disaster-recovery-configuration-options"></a>灾难恢复配置选项摘要

| **配置** | **复原能力** | **RPO** | **RTO** | **工作量** | **成本** |
| --- | --- | --- | --- | --- | --- |
| **主动-主动-主动-n** | 最高 | 0 小时 | 0 小时 | 较低 | 最高 |
| **主动-主动** | 高 | 无 | 无 | 较低 | 高 |
| **主动-热备用服务器** | 中等 | 低 | 低 | 中 | 中 |
| **按需数据恢复群集** | 最低 | 最高 | 最高 | 最高 | 最低 |

## <a name="best-practices"></a>最佳做法

无论选择哪种灾难恢复配置，请遵循以下最佳做法：

* 所有数据库对象、策略和配置都应该保存在源代码管理中，这样就可以从发布自动化工具中将其发布到群集。
* 设计、开发和实现验证例程，以确保从数据角度来看所有群集都是同步的。 Azure 数据资源管理器支持[跨群集联接](kusto/query/cross-cluster-or-database-queries.md?pivots=azuredataexplorer)。 表之间的简单计数或行可帮助验证。
* 使用[连续导出](kusto/management/data-export/continuous-data-export.md)功能并将 Azure 数据资源管理器表中的数据导出到 Azure 数据湖存储。 确保选择的 GRS 可实现最高复原能力。
* 发布过程应该包括可确保实现群集镜像的治理检查和均衡操作。
* 充分了解从头开始构建群集所需完成的所有操作。
* 创建部署单元清单。 你的列表的具体内容取决于你的特定需求，但应包括：部署脚本、引入连接、BI 工具和其他重要配置。 

## <a name="next-steps"></a>后续步骤

通过[使用 Azure 数据资源管理器创建业务连续性和灾难恢复解决方案](business-continuity-create-solution.md)了解详细信息。
