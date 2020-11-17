---
title: 在 Azure Cosmos DB API for MongoDB 资源上预配吞吐量
description: 了解如何在 Azure Cosmos DB API for MongoDB 资源中预配容器、数据库和自动缩放吞吐量。 你将使用 Azure 门户、CLI、PowerShell 和其他各种 SDK。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
origin.date: 10/15/2020
author: rockboyfor
ms.date: 11/09/2020
ms.testscope: yes|no
ms.testdate: 11/09/2020null
ms.author: v-yeche
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: ba6620beb68c53edde4da4c83cd8078607a470a0
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329384"
---
<!--Verified successfully on portal-->
<!--Verified successfully on 3.6 version-->
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-api-for-mongodb-resources"></a>在 Azure Cosmos DB API for MongoDB 资源上预配数据库、容器或自动缩放吞吐量
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

本文介绍如何在 Azure Cosmos DB API for MongoDB 中预配吞吐量。 你可在一个容器或数据库上预配标准（手动）或自动缩放吞吐量，并在数据库中的容器之间共享。 你可使用 Azure 门户、Azure CLI 或 Azure Cosmos DB SDK 来预配吞吐量。

如果你使用不同的 API，请参阅 [SQL API](how-to-provision-container-throughput.md)、[Cassandra API](how-to-provision-throughput-cassandra.md)、[Gremlin API](how-to-provision-throughput-gremlin.md) 文章来预配吞吐量。

<a name="portal-mongodb"></a>
## <a name="azure-portal"></a>Azure 门户

1. 登录 [Azure 门户](https://portal.azure.cn/)。

1. [创建新的 Azure Cosmos 帐户](create-mongodb-dotnet.md#create-a-database-account)，或选择现有的 Azure Cosmos 帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建集合” 。 接下来，请提供以下详细信息：

    * 表明要创建新数据库还是使用现有数据库。 如果要在数据库级别预配吞吐量，请选择“预配数据库吞吐量”选项。
    * 输入集合 ID。
    * 输入分区键值（例如 `/ItemID`）。
    * 输入要预配的吞吐量（例如，1000 RU）
    * 选择“确定”。

    :::image type="content" source="./media/how-to-provision-throughput-mongodb/provision-database-throughput-portal-mongodb-api.png" alt-text="创建具有数据库级吞吐量的新集合时的数据资源管理器的屏幕截图":::

> [!Note]
> 若要在 Azure Cosmos DB 帐户（使用用于 MongoDB 的 Azure Cosmos DB API）为容器预配吞吐量，请使用 `/myShardKey` 作为分区键路径。

<a name="dotnet-mongodb"></a>
## <a name="net-sdk"></a>.NET SDK

```csharp
// refer to MongoDB .NET Driver
// https://docs.mongodb.com/drivers/csharp

// Create a new Client
String mongoConnectionString = "mongodb://DBAccountName:Password@DBAccountName.documents.azure.cn:10255/?ssl=true&replicaSet=globaldb";
mongoUrl = new MongoUrl(mongoConnectionString);
mongoClientSettings = MongoClientSettings.FromUrl(mongoUrl);
mongoClient = new MongoClient(mongoClientSettings);

// Change the database name
mongoDatabase = mongoClient.GetDatabase("testdb");

// Change the collection name, throughput value then update via MongoDB extension commands
// https://docs.azure.cn/cosmos-db/mongodb-custom-commands#update-collection

var result = mongoDatabase.RunCommand<BsonDocument>(@"{customAction: ""UpdateCollection"", collection: ""testcollection"", offerThroughput: 400}");
```

## <a name="azure-resource-manager"></a>Azure 资源管理器

Azure 资源管理器模板可用于在数据库或容器级资源上为所有 Azure Cosmos DB API 预配自动缩放吞吐量。 有关示例，请参阅 [Azure Cosmos DB 的 Azure 资源管理器模板](templates-samples-mongodb.md)。

## <a name="azure-cli"></a>Azure CLI

Azure CLI 可用于在数据库或容器级资源上为所有 Azure Cosmos DB API 预配自动缩放吞吐量。 有关示例，请参阅[用于 Azure Cosmos DB 的 Azure CLI 示例](cli-samples-mongodb.md)。

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell 可用于在数据库或容器级资源上为所有 Azure Cosmos DB API 预配自动缩放吞吐量。 有关示例，请参阅[适用于 Azure Cosmos DB 的 Azure PowerShell 示例](powershell-samples-mongodb.md)。

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何在 Azure Cosmos DB 中预配吞吐量：

* [Azure Cosmos DB 中的请求单位和吞吐量](request-units.md)

<!-- Update_Description: new article about how to provision throughput mongodb -->
<!--NEW.date: 11/09/2020-->