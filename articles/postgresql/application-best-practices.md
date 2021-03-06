---
title: 应用开发最佳做法 - Azure Database for PostgreSQL
description: 了解使用 Azure Database for PostgreSQL 构建应用的最佳做法。
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 12/10/2020
ms.date: 01/11/2021
ms.openlocfilehash: c2d069ab08528f13806e21a806b2dfc7102348c0
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98023992"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-postgresql"></a>使用 Azure Database for PostgreSQL 构建应用程序的最佳做法

下面是一些可帮助你使用 Azure Database for PostgreSQL 构建云就绪应用程序的最佳做法。 这些最佳做法可以减少应用开发时间。

## <a name="configuration-of-application-and-database-resources"></a>应用程序和数据库资源的配置

### <a name="keep-the-application-and-database-in-the-same-region"></a>使应用程序和数据库位于同一区域中
在 Azure 中部署应用程序时，请确保所有依赖项都位于同一区域中。 跨区域分布实例会造成网络延迟，这可能会影响应用程序的总体性能。

### <a name="keep-your-postgresql-server-secure"></a>确保 PostgreSQL 服务器的安全
将 PostgreSQL 服务器配置为[安全](./concepts-security.md)且不能公开访问的服务器。 使用以下选项之一来保护服务器：
- [防火墙规则](./concepts-firewall-rules.md)
- [虚拟网络](./concepts-data-access-and-security-vnet.md)
- [Azure 专用链接](./concepts-data-access-and-security-private-link.md)

为了安全起见，必须始终通过 SSL 连接到 PostgreSQL 服务器，并将 PostgreSQL 服务器和应用程序配置为使用 TLS 1.2。 了解[如何配置 SSL/TLS](./concepts-ssl-connection-security.md)。

### <a name="tune-your-server-parameters"></a>优化服务器参数
对于读取密集型工作负荷优化服务器参数，`tmp_table_size` 和 `max_heap_table_size` 有助于优化性能。 若要计算这些变量所需的值，请查看每连接内存值总计和基本内存。 每连接内存参数（不包括 `tmp_table_size`）的总和与基本内存一起构成了服务器的总内存。

### <a name="use-environment-variables-for-connection-information"></a>使用环境变量获取连接信息
不要在应用程序代码中保存数据库凭据。 根据前端应用程序的不同，按照指南设置环境变量。有关应用服务的使用，请参阅[如何配置应用设置](../app-service/configure-common.md#configure-app-settings)；有关 Azure Kuberentes 服务，请参阅[如何使用 Kubernetes 机密](https://kubernetes.io/docs/concepts/configuration/secret/)。

## <a name="performance-and-resiliency"></a>性能和复原能力
下面是一些可以用来帮助调试应用程序性能问题的工具和做法。

### <a name="use-connection-pooling"></a>使用连接池
使用连接池，在启动时建立一组固定的连接并进行维护。 这还有助于减少由于在数据库服务器上建立动态新连接而导致的服务器上的内存碎片。 如果应用框架或数据库驱动程序支持连接池，则可以在应用程序端对其进行配置。 如果不支持这种情况，另一种建议的方法是利用代理连接池程序服务，如在应用程序外部运行并连接到数据库服务器的 [PgBouncer](https://pgbouncer.github.io/) 或 [Pgpool](https://pgpool.net/mediawiki/index.php/Main_Page)。 PgBouncer 和 Pgpool 都是基于社区的工具，可用于 Azure Database for PostgreSQL。

### <a name="retry-logic-to-handle-transient-errors"></a>用来处理暂时性错误的重试逻辑
你的应用程序可能会遇到暂时性错误：到数据库的连接间歇性断开或丢失。 在这种情况下，服务器在 5 到 10 秒内重试一两次后就会启动并运行。 良好的做法是在第一次重试前等待 5 秒。 然后，每次重试都逐步延长等待时间，最多 60 秒。 限制最大重试次数。达到该次数时，应用程序会认为操作失败，随后你就可以进一步进行调查。 有关详细信息，请参阅[如何排查连接错误](./concepts-connectivity.md)。

### <a name="enable-read-replication-to-mitigate-failovers"></a>启用读取复制以缓解故障转移问题
对于故障转移场景，可以使用[数据传入复制](./concepts-read-replicas.md)。 使用只读副本时，源服务器与副本服务器之间不会自动发生故障转移。你会注意到源服务器和副本服务器之间存在延迟，因为复制是异步的。 网络延迟可能受许多因素影响，例如，在源服务器上运行的工作负荷的大小，以及数据中心之间的延迟。 大多数情况下，副本延迟在几秒钟到几分钟之间。


## <a name="database-schema-and-queries"></a>数据库架构和查询
下面是构建数据库架构和查询时需要注意的一些技巧。

### <a name="use-bigint-or-uuid-for-primary-keys"></a>将 BIGINT 或 UUID 用作主键
在构建自定义应用程序或某些框架时，它们可能将 `INT` 而不是 `BIGINT` 用作主键。 使用 ```INT``` 时，存在数据库中的值超出 ```INT``` 数据类型的存储容量的风险。 对现有生产应用程序进行此更改可能会花费更多的开发时间。 另一种方法是将 [UUID](https://www.postgresql.org/docs/current/datatype-uuid.html) 用作主键。此标识符使用自动生成的 128 位字符串，例如 ```a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11```。 详细了解 [PostgreSQL 数据类型](https://www.postgresql.org/docs/8.1/datatype.html)。

### <a name="use-indexes"></a>使用索引

Postgres 中有多种类型的[索引](https://www.postgresql.org/docs/9.1/indexes.html)，可通过不同方式使用。 使用索引时服务器查找和检索特定行的速度比不使用索引时快。 但索引也会增加数据库服务器的开销，因此应避免使用过多索引。

### <a name="use-autovacuum"></a>使用 autovacuum
可以在 Azure Database for PostgreSQL 服务器上使用 autovacuum 优化服务器。 PostgreSQL 支持更好的数据库并发，但每次更新都会导致插入和删除。 对于删除，记录将被软标记，稍后被清除。 为执行这些任务，PostgreSQL 将运行一个清扫作业。 如果不时常运行清扫作业，累积的死元组可能会导致以下问题：

- 数据膨胀，例如数据库和表变大。
- 更大的非最优索引。
- I/O 增加。

详细了解[如何使用 autovacuum 进行优化](howto-optimize-autovacuum.md)。

### <a name="use-pg_stats_statements"></a>使用 pg_stats_statements
Pg_stat_statements 是 PostgreSQL 扩展，默认情况下在 Azure Database for PostgreSQL 中启用  。 该扩展提供了一种方法来跟踪服务器执行的所有 SQL 语句的执行统计信息。 请参阅[如何使用 pg_statement](howto-optimize-query-stats-collection.md)。


### <a name="use-the-query-store"></a>使用查询存储
Azure Database for PostgreSQL 中的[查询存储](./concepts-query-store.md)功能提供了用于跟踪查询统计信息的更高效的方法。 建议使用此功能作为使用 pg_stats_statements 的替代方法  。

### <a name="optimize-bulk-inserts-and-use-transient-data"></a>优化批量插入并使用临时数据
如果工作负荷操作涉及到瞬态数据或者需要批量插入大型数据集，可以考虑使用无日志记录表。 它在默认情况下提供原子性和持久性。 原子性、一致性、隔离性和持久性组成了 ACID 属性。 请参阅[如何优化批量插入](howto-optimize-bulk-inserts.md)。

## <a name="next-steps"></a>后续步骤
[Postgres 指南](http://postgresguide.com/)
