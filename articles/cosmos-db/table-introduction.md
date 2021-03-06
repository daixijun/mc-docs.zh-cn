---
title: Azure Cosmos DB 表 API 简介
description: 了解如何通过 Azure Tables API 使用 Azure Cosmos DB 以低延迟存储和查询大量键值对数据。
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
origin.date: 11/25/2020
author: rockboyfor
ms.date: 01/18/2021
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.openlocfilehash: d85ac0d879231ebc52c2db77a8689224a6d04380
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98230539"
---
<!--Verify sucessfully-->
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Azure Cosmos DB 简介：表 API
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[Azure Cosmos DB](introduction.md) 提供的表 API 适用于为 Azure 表存储编写且需要以下高级功能的应用程序：

* [统包式多区域分布](distribute-data-globally.md)。
* 中国各地[专用吞吐量](partitioning-overview.md)（使用预配的吞吐量时）。
* 99% 的情况下低至个位数的毫秒级延迟。
* 保证高可用性。
* 自动编制辅助索引。

为 Azure 表存储编写的应用程序无需更改代码便可使用表 API 迁移到 Azure Cosmos DB，并可充分利用高级功能。 表 API 包含可用于 .NET、Java、Python 和 Node.js 的客户端 SDK。

> [!NOTE]
> [无服务器容量模式](serverless.md)现在在 Azure Cosmos DB 的表 API 上可用。

> [!IMPORTANT]
> .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) 目前处于维护模式，不久将被弃用。 请升级到新的 .NET Standard 库 [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) 来继续获取表 API 支持的最新功能。

## <a name="table-offerings"></a>表产品/服务
如果当前使用 Azure 表存储，那么改用 Azure Cosmos DB 表 API 可以获得以下好处：

| Feature | Azure 表存储 | Azure Cosmos DB 表 API |
| --- | --- | --- |
| 延迟 | 快速，但对延迟没有上限。 | 读取操作和写入操作的低至个位数的毫秒级延迟（通过以下统计数据提供支持：在中国的任何位置，对于任何规模，99% 的情况下读取和写入操作的延迟 < 10 毫秒）。 |
| 吞吐量 | 可变吞吐量模型。 表的可伸缩性限制为 20,000 个操作/秒。 | 使用 SLA 支持的[每个表专用保留吞吐量](request-units.md)实现高度可缩放。 帐户没有吞吐量上限，每个表支持 >1000 万个操作/秒。 |
| 多区域分布 | 具有一个可选可读辅助读取区域以实现高可用性的单一区域。 | 从一个区域到任意数量区域的[统包式多区域分布](distribute-data-globally.md)。 支持在中国各地随时[自动和手动故障转移](high-availability.md)。 多个写入区域，允许任何区域接受写入操作。 |
| 索引 | 仅对 PartitionKey 和 RowKey 建立主索引。 没有辅助索引。 | 默认情况下，自动对所有属性完成编制索引，不进行索引管理。 |
| 查询 | 执行查询时使用主键的索引，否则进行扫描。 | 查询可以利用属性的自动索引缩短查询时间。 |
| 一致性 | 在主要区域内实现强一致性。 在辅助区域内实现最终一致性。 | [五个定义完善的一致性级别](consistency-levels.md)可基于应用程序需要权衡可用性、延迟、吞吐量和一致性。 |
| 定价 | 基于使用量。 | 提供[基于使用量](serverless.md)和[预配容量](set-throughput.md)两种模式。 |
| SLA | 可用性为 99.9% 至 99.99%，具体取决于复制策略。 | 单区域帐户的读取可用性为 99.999%、写入可用性为 99.99%，多区域帐户的写入可用性为 99.999%。 [综合性 SLA](https://www.azure.cn/support/sla/cosmos-db/)：涵盖可用性、延迟、吞吐量和一致性。 |

## <a name="get-started"></a>入门

在 [Azure 门户](https://portal.azure.cn)中创建 Azure Cosmos DB 帐户。 然后开始使用我们的快速入门：[通过 .NET 使用表 API](create-table-dotnet.md)。 

> [!IMPORTANT]
> 如果已在预览期间创建表 API 帐户，请[新建表 API 帐户](create-table-dotnet.md#create-a-database-account)，这样才能使用正式版表 API SDK。
>

## <a name="next-steps"></a>后续步骤

下面是一些可帮助你入门的指南：
* [使用表 API 生成 .NET 应用程序](create-table-dotnet.md)
* [在 .NET 中使用表 API 进行开发](tutorial-develop-table-dotnet.md)
* [使用表 API 查询表数据](tutorial-query-table.md)
* [了解如何使用表 API 设置 Azure Cosmos DB 多区域分发](tutorial-global-distribution-table.md)
* [Azure Cosmos DB 表 .NET Standard SDK](table-sdk-dotnet-standard.md)
* [Azure Cosmos DB 表 .NET SDK](table-sdk-dotnet.md)
* [Azure Cosmos DB 表 Java SDK](table-sdk-java.md)
* [Azure Cosmos DB 表 Node.js SDK](table-sdk-nodejs.md)
* [用于 Python 的 Azure Cosmos DB 表 SDK](table-sdk-python.md)

<!-- Update_Description: update meta properties, wording update, update link -->