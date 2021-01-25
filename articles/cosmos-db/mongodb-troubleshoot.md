---
title: 排查 Azure Cosmos DB 的 API for Mongo DB 中的常见错误
description: 本文档讨论解决 Azure Cosmos DB 的 API for MongoDB 中遇到的常见问题的方法。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
origin.date: 07/15/2020
author: rockboyfor
ms.date: 01/18/2021
ms.testscope: yes
ms.testdate: 08/10/2020
ms.author: v-yeche
ms.openlocfilehash: 292a68804beefe591482a9fabf282f57815e51d0
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98230298"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>解决 Azure Cosmos DB 的 API for MongoDB 中的常见问题
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

下面的文章介绍了使用 Azure Cosmos DB API for MongoDB 的部署的常见错误和解决方案。

>[!Note]
> Azure Cosmos DB 不托管 MongoDB 引擎。 它提供了 MongoDB 线路协议的实现。 因此，其中的一些错误仅出现在 Azure Cosmos DB API for MongoDB 中。 

## <a name="common-errors-and-solutions"></a>常见错误和解决方法

| 代码       | 错误                | 说明  | 解决方案  |
|------------|----------------------|--------------|-----------|
| 2 | 与指定的 order-by 项对应的索引路径被排除，或者 order by 查询没有可以通过其来为它提供服务的对应复合索引。 | 查询请求对未建立索引的字段进行排序。 | 为所尝试的排序查询创建匹配索引（或复合索引）。 |
| 13 | 未授权 | 请求缺少权限，无法完成。 | 请确保为数据库和集合设置正确的权限。  |
| 16 | InvalidLength | 指定的请求的长度无效。 | 如果使用 explain() 函数，请确保只提供一个操作。 |
| 26 | NamespaceNotFound | 找不到查询中所引用的数据库或集合。 | 请确保你的数据库/集合名称与查询中的名称完全匹配。|
| 50 | ExceededTimeLimit | 请求已超过 60 秒执行时间的超时。 |  此问题的原因可能有很多。 其中一个原因是当前分配的请求单位容量不足，无法完成请求。 可以通过增加该集合或数据库的请求单位来解决此问题。 其他情况下，可以通过将大型请求拆分为较小请求来规避此问题。|
| 61 | ShardKeyNotFound | 请求中的文档未包含集合的分片键（Azure Cosmos DB 分区键）。 | 请确保在请求中使用集合的分片键。|
| 66 | ImmutableField | 请求试图更改不可变字段 | “id”字段是不可变的。 请确保请求不要试图更新该字段。 |
| 67 | CannotCreateIndex | 无法完成创建索引的请求。 | 在每个容器中最多可以创建 500 个单字段索引。 复合索引中最多可以包含八个字段（版本 3.6+ 支持复合索引）。 |
| 115 | CommandNotSupported | 不支持所尝试的请求。 | 错误中应当提供了更多详细信息。 如果此功能对你的部署很重要，请在 [Azure 门户](https://portal.azure.cn/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)中创建一个支持票证来告知我们。 |
| 11000 | DuplicateKey | 集合中已存在要插入的文档的分片键（Azure Cosmos DB 分区键），或者违反了唯一索引字段约束。 | 请使用 update() 函数更新现有文档。 如果违反了唯一索引字段约束，则对文档执行插入或更新操作时请使用分片/分区中尚不存在的字段值。 |
| 16500 | TooManyRequests  | 使用的请求单位总数超过了集合的预配请求单位率，已达到限制。 | 请考虑从 Azure 门户对分配给一个容器或一组容器的吞吐量进行缩放，也可以重试该操作。 如果启用 SSR（服务器端重试），则 Azure Cosmos DB 会自动重试由于此错误而失败的请求。 |
| 16501 | ExceededMemoryLimit | 作为一种多租户服务，操作已超出客户端的内存配额。 | 通过限制性更强的查询条件缩小操作的作用域，或者通过 [Azure 门户](https://portal.azure.cn/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)联系技术支持。 示例： `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | 无法识别的管道阶段名称。 | 聚合管道请求中的阶段名称无法识别。 | 请确保你的请求中的所有聚合管道名称都有效。 |
| - | MongoDB 线路版本问题 | 旧版本的 MongoDB 驱动程序无法在连接字符串中检测 Azure Cosmos 帐户的名称。 | 在 Cosmos DB 的 API for MongoDB 连接字符串末尾追加 appName=@**accountName**@，其中 ***accountName*** 是 Cosmos DB 帐户名。 |

## <a name="next-steps"></a>后续步骤

- 了解如何将 [Studio 3T](mongodb-mongochef.md) 与 Azure Cosmos DB 的用于 MongoDB 的 API 配合使用。
- 了解如何将 [Robo 3T](mongodb-robomongo.md) 与 Azure Cosmos DB 的用于 MongoDB 的 API 配合使用。
- 通过 Azure Cosmos DB 的用于 MongoDB 的 API 来浏览 MongoDB [示例](mongodb-samples.md)。

<!-- Update_Description: update meta properties, wording update, update link -->