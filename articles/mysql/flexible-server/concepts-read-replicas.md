---
title: 只读副本 - Azure Database for MySQL - 灵活服务器
description: 了解 Azure Database for MySQL 灵活服务器中的只读副本：创建副本、连接到副本、监视复制和停止复制。
author: WenJason
ms.author: v-jay
ms.service: mysql
ms.topic: conceptual
origin.date: 10/26/2020
ms.date: 01/11/2021
ms.openlocfilehash: 418edc86ac5ddf288b89920a201cfd8335bf4792
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98023898"
---
# <a name="read-replicas-in-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL 灵活服务器中的只读副本

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器中的只读副本现处于预览状态。

MySQL 是一种常用的数据库引擎，用于运行 Internet 规模的 Web 和移动应用程序。 许多客户将其用于在线教育服务、视频流式处理服务、数字支付解决方案、电子商务平台、游戏服务、新闻门户、政府和医疗保健网站。 这些服务需要随着 Web 或移动应用程序流量的增加而服务和扩展。

在应用程序端，应用程序通常是用 Java 或 php 开发的，并迁移到在 Azure 虚拟机规模集或 Azure 应用服务上运行，或者进行容器化以在 Azure Kubernetes 服务 (AKS) 上运行。 使用虚拟机规模集、应用服务或 AKS 作为底层基础结构，可以通过即时预配新的 VM 并复制应用程序的无状态组件来满足请求，以简化应用程序的缩放，但是数据库通常会成为集中式有状态组件的瓶颈。

使用只读副本功能可将数据从 Azure Database for MySQL 灵活服务器复制到只读服务器。 可将源服务器中的数据复制到最多 10 个副本。 使用 MySQL 引擎的基于本机二进制日志 (binlog) 文件位置的复制技术以异步方式更新副本。 若要了解有关 binlog 复制的详细信息，请参阅 [MySQL binlog 复制概述](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)。

副本是新的服务器，可像管理源 Azure Database for MySQL 灵活服务器一样对其进行管理。 每个只读副本按照预配计算资源的 vCore 数量以及每月 GB 存储量计费。 有关详细信息，请参阅[定价](./concepts-compute-storage.md#pricing)。

如需了解有关 MySQL 复制功能和问题的详细信息，请参阅 [MySQL 复制文档](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)。

## <a name="common-use-cases-for-read-replica"></a>只读副本的常见用例

只读副本功能可帮助改善读取密集型工作负荷的性能与规模。 读取工作负载可与副本隔离，而写入工作负载可定向到源。

常见方案是：

* 使用轻量级连接代理（例如 [ProxySQL](https://aka.ms/ProxySQLLoadBalanceReplica)）或基于微服务的模式扩展来自应用程序的读取工作负载，从而将来自应用程序的读取查询横向扩展到只读副本
* BI 或分析报告工作负载可使用只读副本用作数据源来进行报告
* 对于 IoT 或制造方案，其中遥测信息被引入到 MySQL 数据库引擎，而多个只读副本用于报告数据

副本是只读的，因此不能直接缓解源上的写入容量负担。 此功能并非面向写入密集型工作负荷。

只读副本功能使用 MySQL 本机异步复制。 该功能不适用于同步复制方案。 源与副本之间将会存在明显的延迟。 副本上的数据最终将与源上的数据保持一致。 对于能够适应这种延迟的工作负荷，可以使用此功能。

## <a name="create-a-replica"></a>创建副本

如果源服务器没有现有的副本服务器，该源服务器会先重启，以便为复制做好自身准备。

启动“创建副本”工作流时，将创建空白的 Azure Database for MySQL 服务器。 新服务器中会填充源服务器上的数据。 创建时间取决于源服务器上的数据量，以及自上次每周完整备份以来所经历的时间。 具体所需时间从几分钟到几小时不等。

> [!NOTE]
> 只读副本是使用与源服务器相同的服务器配置创建的。 副本服务器配置在创建后可以更改。 始终会在与源服务器相同的资源组、位置和订阅中创建副本服务器。 如果要将副本服务器创建到不同的资源组或不同的订阅，可以在创建后[移动副本服务器](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。 建议副本服务器的配置应始终采用与源服务器相同或更大的值，以确保副本能够与源服务器保持一致。

了解如何[在 Azure 门户中创建只读副本](how-to-read-replicas-portal.md)。

## <a name="connect-to-a-replica"></a>连接到副本

创建时，副本会继承源服务器的连接方法。 不能更改副本的连接方法。 例如，如果源服务器具有专用访问（VNet 集成），那么副本不能处于公共访问权限（允许的 IP 地址） 。

副本会从源服务器继承管理员帐户。 源服务器上的所有用户帐户都会复制到只读副本。 只能通过使用源服务器上可用的用户帐户来连接到只读副本。

可使用主机名和有效的用户帐户连接到副本，就像在常规的 Azure Database for MySQL 灵活服务器上连接一样。 对于名称为 **myreplica**、管理员用户名为 **myadmin** 的服务器，可以使用 mysql CLI 连接到副本：

```bash
mysql -h myreplica.mysql.database.chinacloudapi.cn -u myadmin -p
```

在提示符下，输入用户帐户的密码。

## <a name="monitor-replication"></a>监视复制

Azure Database for MySQL 灵活服务器在 Azure Monitor 中提供“复制滞后时间(秒)”指标。 此指标仅适用于副本。 此指标是使用 MySQL 的 `SHOW SLAVE STATUS` 命令中提供的 `seconds_behind_master` 指标计算的。 请设置警报，以便在复制滞后时间达到工作负荷不可接受的值时收到通知。

如果发现复制滞后时间增加，请参阅[排查复制延迟问题](./../howto-troubleshoot-replication-latency.md)，以便排除故障并了解可能的原因。

## <a name="stop-replication"></a>停止复制

可以停止源与副本之间的复制。 在源服务器与只读副本之间停止复制后，该副本会成为独立服务器。 独立服务器中的数据是启动“停止复制”命令时副本上可用的数据。 独立服务器不会与源服务器保持同步。

选择停止复制到副本时，它会丢失指向其以前的源和其他副本的所有链接。 在源与其副本之间无法自动进行故障转移。

> [!IMPORTANT]
> 独立服务器不能再次成为副本。
> 在只读副本上停止复制之前，请确保副本包含所需的全部数据。

了解如何[停止复制到副本](how-to-read-replicas-portal.md)。

## <a name="failover"></a>故障转移

在源服务器和副本服务器之间无法自动进行故障转移。 

只读副本适用于扩展读取密集型工作负载，但并不用于满足服务器的高可用性需求。 在源服务器和副本服务器之间无法自动进行故障转移。 执行这种手动故障转移的方式是，停止只读副本上的复制以使其在读写模式下处于联机状态。

由于复制是异步的，因此在源和副本之间存在滞后时间。 滞后时间量会受到多种因素影响，例如，在源服务器上运行的工作负荷有多大，以及数据中心之间的延迟有多严重。 大多数情况下，副本验证在几秒钟到几分钟之间。 可以使用“副本延迟”指标来跟踪实际的副本延迟，该指标适用于每个副本。 该指标显示的是自上次重播事务以来所经历的时间。 建议观察一段时间的副本延迟，以便确定平均延迟。 可以针对副本延迟设置警报，这样，当它超出预期范围时，你就可以采取行动。

> [!Tip]
> 如果你故障转移到副本，在取消副本与源的链接时的滞后时间将表明会丢失多少数据。

一旦决定要故障转移到某个副本， 

1. 请停止将数据复制到副本<br/>
   此步骤是使副本服务器能够接受写入所必需的。 在此过程中，副本服务器会取消与源的链接。 启动停止复制的操作后，后端进程通常需要大约 2 分钟才能完成。 请参阅本文的[停止复制](#stop-replication)部分，了解此操作的潜在影响。
    
2. 将应用程序指向（以前的）副本<br/>
   每个服务器都有唯一的连接字符串。 更新应用程序，使之指向（以前的）副本而不是源。
    
如果应用程序成功处理了读取和写入操作，则表明故障转移已完成。 应用程序经历的停机时间取决于何时检测到问题并完成上面的步骤 1 和 2。

## <a name="considerations-and-limitations"></a>注意事项和限制

| 方案 | 限制/注意事项 |
|:-|:-|
| 跨区域只读复制 | 不支持 |
| 定价 | 运行副本服务器的成本取决于副本服务器的运行区域 |
| 源服务器重启 | 在为没有现有副本的源创建副本时，该源服务器会先重启，以便为复制做好自身准备。 请考虑这一点并在非高峰期执行这些操作 |
| 新副本 | 只读副本被创建为新的 Azure Database for MySQL 灵活服务器。 无法将现有的服务器设为副本。 无法创建另一个只读副本的副本 |
| 副本配置 | 副本是使用与源相同的服务器配置创建的。 在创建副本后，可以独立于源服务器更改多项设置：计算代系、vCore 数、存储，以及备份保持期。 也可单独更改计算层。<br> <br> **重要事项**  <br> - 将源服务器的配置更新为新值之前，请将副本配置更新为与这些新值相等或更大的值。 此操作可确保副本与源服务器发生的任何更改保持同步。 <br/> 在创建副本时，连接方法和参数设置会从源服务器继承到副本。 之后，副本的规则便会独立。 |
| 停止的副本 | 如果停止源服务器与只读副本之间的复制，已停止的副本会成为接受读取和写入操作的独立服务器。 独立服务器不能再次成为副本。 |
| 已删除的源服务器和独立服务器 | 在删除源服务器时，会对所有只读副本都停止复制。 这些副本会自动成为独立服务器，并且可以接受读取和写入。 源服务器本身会被删除。 |
| 用户帐户 | 源服务器上的用户会复制到只读副本。 只能使用源服务器上可用的用户帐户来连接到只读副本。 |
| 服务器参数 | 为了防止数据不同步并避免潜在的数据丢失或损坏，使用读取副本时，会锁定某些服务器参数以防止其更新。 <br> 源服务器和副本服务器上都会锁定以下服务器参数：<br> - [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html) <br> - [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) <br> 副本服务器上的 [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) 参数处于锁定状态。 <br> 若要在源服务器上更新上述某个参数，请删除副本服务器，更新源上的参数值，然后重新创建副本。 |
| 其他 | - 不支持创建副本服务器的副本。 <br> - 内存中的表可能会导致副本服务器变得不同步。这是 MySQL 复制技术的限制。 有关详细信息，请阅读 [MySQL 参考文档](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html)中的更多信息。 <br>- 确保源服务器表具有主键。 缺少主键可能会导致源和副本之间出现复制延迟。<br>- 查看 [MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)中 MySQL 复制限制的完整列表 |

## <a name="next-steps"></a>后续步骤

- 了解如何[使用 Azure 门户创建和管理只读副本](how-to-read-replicas-portal.md)
- 了解如何[使用 Azure CLI 创建和管理只读副本](how-to-read-replicas-cli.md)