---
title: Azure Cache for Redis 的高可用性
description: 了解 Azure Cache for Redis 高可用性功能和选项
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: v-junlch
ms.openlocfilehash: 3b9323d3ff908c50c5040b460c8762dd712d2480
ms.sourcegitcommit: 33f2835ec41ca391eb9940edfcbab52888cf8a01
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94326545"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Azure Cache for Redis 的高可用性

Azure Cache for Redis 具有内置的高可用性。 其高可用性体系结构的目标是确保托管的 Redis 实例正常运行，即使其基础虚拟机 (VM) 受计划内或计划外中断的影响。 它提供的可用性百分比率要远高于将 Redis 承载在单个 VM 上的情况。

Azure Cache for Redis 使用多个称为“节点”的用于缓存的 VM 来实现高可用性。 它将这些节点配置为以协调的方式进行数据复制和故障转移。 它还会协调维护操作，例如 Redis 软件修补。 “标准”、“高级”和“企业”层级中提供了各种高可用性选项：

| 选项 | 说明 | 可用性 | 标准 | 高级 | Enterprise |
| ------------------- | ------- | ------- | :------: | :---: | :---: |
| [标准复制](#standard-replication)| 单个数据中心或可用性区域 (AZ) 中具有自动故障转移功能的双节点复制配置 | 99.9% |✔|✔|-|
| [Enterprise 群集](#enterprise-cluster) | 链接了两个区域中的缓存实例，提供自动故障转移 | 99.9% |-|-|✔|
| [异地复制](#geo-replication) | 两个区域中的链接缓存实例，具有用户控制的故障转移 | 99.9%（对于单个区域） |-|✔|-|

## <a name="standard-replication"></a>标准复制

默认情况下，在“标准”或“高级”层级中，Azure Cache for Redis 在一对 Redis 服务器上运行。 这两个服务器托管在专用 VM 上。 开源 Redis 只允许一台服务器处理数据写入请求。 此服务器是主要节点，而另一服务器是副本。 预配服务器节点后，Azure Cache for Redis 可向其分配主要角色和副本角色。 主要节点通常负责为来自 Redis 客户端的写入和读取请求提供服务。 在执行写入操作时，它会向其内部内存提交一个新密钥和密钥更新，并立即回复客户端。 它以异步方式将操作转发给副本。

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="数据复制设置":::
   
>[!NOTE]
>通常，Redis 客户端会与 Redis 缓存中的主节点通信，以获取所有读取和写入请求。 某些 Redis 客户端可以配置为从副本节点进行读取。
>
>

如果 Redis 缓存中的主节点不可用，则副本会自动升级为新的主节点。 此过程称为故障转移。 副本会等待足够长的时间才去接管，以免出现主节点快速恢复的情况。 发生故障转移时，Azure Cache for Redis 会预配新的 VM，并将其作为副本节点加入到缓存中。 副本执行与主节点的完整数据同步，这样它就会有缓存数据的另一个副本。

主节点在计划内维护活动（例如 Redis 软件或操作系统更新）中可能会停止服务。 它还可能因为计划外事件（例如底层硬件、软件或网络故障）而停止工作。 [Azure Cache for Redis 的故障转移和修补](cache-failover.md)提供了有关 Redis 故障转移类型的详细说明。 Azure Cache for Redis 在其生存期内会经历许多故障转移。 高可用性体系结构旨在使缓存中的这些更改对其客户端尽可能透明。

>[!NOTE]
>以下内容以预览版提供。
>
>

此外，Azure Cache for Redis 在高级层中允许更多副本节点。 可以为[多副本缓存](cache-how-to-multi-replicas.md)配置最多三个副本节点。 具有更多副本通常会提高复原能力，因为附加的节点会为主节点提供备份。 即使有更多副本，Azure Cache for Redis 实例仍可能会受到数据中心范围的或 AZ 范围的服务中断的严重影响。 通过将多个副本与[区域冗余](#zone-redundancy)结合使用，可以提高缓存可用性。

## <a name="enterprise-cluster"></a>Enterprise 群集

>[!NOTE]
>此功能以预览版提供。
>
>

任一企业层中的缓存在 Redis Enterprise 群集上运行。 它需要在任何时候都保持奇数个服务器节点以便形成仲裁。 默认情况下，它由三个节点组成，每个节点都托管在专用 VM 上。 Enterprise 缓存有两个大小相同的数据节点和一个较小的仲裁节点 。 Enterprise Flash 缓存有三个大小相同的数据节点。 Enterprise 群集在内部将 Redis 数据划分为多个分区。 每个分区有一个主分区和至少一个副本 。 每个数据节点都包含一个或多个分区。 Enterprise 群集可确保任何分区的主分区和副本永远不会并置在同一数据节点上。 分区将数据从主分区异步复制到其相应的副本。

当数据节点不可用或发生网络拆分时，会发生类似于[标准复制](#standard-replication)中描述的故障转移。 Enterprise 群集使用基于仲裁的模型来确定哪些未受影响的节点将参与新的仲裁。 它还根据需要将这些节点中的分区副本提升为主分区。


## <a name="geo-replication"></a>异地复制

异地复制主要是为灾难恢复设计的。 它使你能够在另一 Azure 区域中配置 Azure Cache for Redis 实例，以便为主缓存提供备份。 [为 Azure Cache for Redis 设置异地复制](cache-how-to-geo-replication.md)提供了有关异地复制工作原理的详细说明。

## <a name="next-steps"></a>后续步骤

详细了解如何配置 Azure Cache for Redis 高可用性选项。

* [Azure Cache for Redis 高级服务层](cache-overview.md#service-tiers)
* [将副本添加到 Azure Cache for Redis](cache-how-to-multi-replicas.md)
* [为 Azure Cache for Redis 设置异地复制](cache-how-to-geo-replication.md)

