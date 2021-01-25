---
title: 有关适用于 MongoDB 的 Azure Cosmos DB API 的常见问题解答
description: 获取有关适用于 MongoDB 的 Azure Cosmos DB API 的常见问题解答
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
origin.date: 04/28/2020
author: rockboyfor
ms.date: 01/18/2021
ms.author: v-yeche
ms.openlocfilehash: f950ac705286fcef86ad0cc33ebbc2ad9c1dd9b8
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98230331"
---
<!--Verified successfully, ONLY CHARACTER CONTENT-->
# <a name="frequently-asked-questions-about-the-azure-cosmos-dbs-api-for-mongodb"></a>有关适用于 MongoDB 的 Azure Cosmos DB API 的常见问题解答
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB 的 API for MongoDB 是一个线路协议兼容层，允许应用程序使用现有的、社区支持的 SDK 和用于 MongoDB 的驱动程序轻松、透明地与本机 Azure Cosmos 数据库引擎通信。 开发人员现在可以使用现有的 MongoDB 工具链和技术，生成能够充分利用 Azure Cosmos DB 的应用程序。 开发人员会受益于 Azure Cosmos DB 的独特功能，其中包括带多区域写入复制功能的多区域分发、自动索引、备份维护、提供财务支持的服务级别协议 (SLA) 等。

## <a name="how-do-i-connect-to-my-database"></a>如何连接到数据库？

若要通过 Azure Cosmos DB 的用于 MongoDB 的 API 连接到 Cosmos 数据库，最快捷的方法是使用 [Azure 门户](https://portal.azure.cn)。 转到帐户，然后在左侧导航菜单上单击“快速启动”。 快速入门是获取连接到数据库的代码片段的最佳方式。

Azure Cosmos DB 实施严格的安全要求和标准。 Azure Cosmos DB 帐户需要通过 TLS 进行身份验证和安全通信，因此务必使用 TLSv1.2。

有关详细信息，请参阅[通过 Azure Cosmos DB 的用于 MongoDB 的 API 连接到 Cosmos 数据库](connect-mongodb-account.md)。

## <a name="error-codes-while-using-azure-cosmos-dbs-api-for-mongodb"></a>在使用适用于 MongoDB 的 Azure Cosmos DB API 时出现错误代码？

除常见的 MongoDB 错误代码外，Azure Cosmos DB 的用于 MongoDB 的 API 还有自己的特定错误代码。 可在[故障排除指南](mongodb-troubleshoot.md)中找到这些代码。

## <a name="supported-drivers"></a>支持的驱动程序

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>是否支持将 MongoDB 的 Simba 驱动程序与 Azure CosmosDB 的用于 MongoDB 的 API 一起使用？

是的，可以将 Simba 的 Mongo ODBC 驱动程序与 Azure Cosmos DB 的用于 MongoDB 的 API 一起使用

## <a name="next-steps"></a>后续步骤

* [使用适用于 MongoDB 的 Azure Cosmos DB API 构建 .NET Web 应用](create-mongodb-dotnet.md)
* [在 Azure Cosmos DB 中使用 Java 和 MongoDB API 创建控制台应用](create-mongodb-java.md)

<!-- Update_Description: update meta properties, wording update, update link -->