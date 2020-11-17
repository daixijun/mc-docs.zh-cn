---
title: 在 Azure Cosmos DB SQL API 中预配容器吞吐量
description: 了解如何使用 Azure 门户、CLI、PowerShell 和各种其他 SDK 在 Azure Cosmos DB SQL API 中预配容器级别的吞吐量。
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
origin.date: 10/14/2020
author: rockboyfor
ms.date: 11/09/2020
ms.testscope: yes
ms.testdate: 09/28/2020
ms.author: v-yeche
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: aa9ca46d61fe3e10382a9e99118cae0c695cc7d2
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94328661"
---
# <a name="provision-standard-manual-throughput-on-an-azure-cosmos-container---sql-api"></a>在 Azure Cosmos 容器上预配标准（手动）吞吐量 - SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

本文说明了如何在 Azure Cosmos DB SQL API 的容器中预配标准（手动）吞吐量。 可以为单个容器预配吞吐量，也可以[为数据库预配吞吐量](how-to-provision-database-throughput.md)，并在数据库中的容器之间共享。 可以使用 Azure 门户、Azure CLI 或 Azure Cosmos DB SDK 为容器预配吞吐量。

如果你使用其他 API，请参阅[用于 MongoDB 的 API](how-to-provision-throughput-mongodb.md)、[Cassandra API](how-to-provision-throughput-cassandra.md)、[Gremlin API](how-to-provision-throughput-gremlin.md) 文章来预配吞吐量。

## <a name="azure-portal"></a>Azure 门户

1. 登录 [Azure 门户](https://portal.azure.cn/)。

1. [创建新的 Azure Cosmos 帐户](create-sql-api-dotnet.md#create-account)，或选择现有的 Azure Cosmos 帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建容器” 。 接下来，请提供以下详细信息：

    * 表明要创建新数据库还是使用现有数据库。
    * 输入容器 ID。
    * 输入分区键值（例如 `/ItemID`）。
    * 输入要预配的吞吐量（例如，1000 RU）
    * 选择“确定” 。

    :::image type="content" source="./media/how-to-provision-container-throughput/provision-container-throughput-portal-sql-api.png" alt-text="数据资源管理器的屏幕截图，突出显示“新建集合”":::

## <a name="azure-cli-or-powershell"></a>Azure CLI 或 PowerShell

若要创建具有专用吞吐量的容器，请参阅

* [使用 Azure CLI 创建容器](manage-with-cli.md#create-a-container)
* [使用 PowerShell 创建容器](manage-with-powershell.md#create-container)

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> 使用适用于 SQL API 的 Cosmos SDK 为除 Cassandra 和 MongoDB API 之外的所有 Cosmos DB API 预配吞吐量。

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
// Create a container with a partition key and provision throughput of 1000 RU/s
string containerName = "myContainerName";
string partitionKeyPath = "/myPartitionKey";

await this.cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(
    id: containerName,
    partitionKeyPath: partitionKeyPath,
    throughput: 1000);

```

---

## <a name="javascript-sdk"></a>JavaScript SDK

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "containerId",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何在 Azure Cosmos DB 中预配吞吐量：

* [如何在数据库上预配标准（手动）吞吐量](how-to-provision-database-throughput.md)
* [如何在数据库上预配自动缩放吞吐量](how-to-provision-autoscale-throughput.md)
* [Azure Cosmos DB 中的请求单位和吞吐量](request-units.md)

<!-- Update_Description: update meta properties, wording update, update link -->