---
title: 用于 SQL API 的 Spring Data Azure Cosmos DB v3 的发行说明和资源
description: 了解用于 SQL API 的 Spring Data Azure Cosmos DB v3 的信息，包括发行日期、停用日期以及在 Azure Cosmos DB SQL Async Java SDK 各版本之间所做的更改。
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
origin.date: 08/18/2020
author: rockboyfor
ms.date: 11/16/2020
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.custom: devx-track-java
ms.openlocfilehash: 5761cce1a60f45ee2bd0caf1a0a05fe24b6e0d6b
ms.sourcegitcommit: 5f07189f06a559d5617771e586d129c10276539e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94552844"
---
<!--Verified successfully-->
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>适用于 Core (SQL) API 的 Spring Data Azure Cosmos DB v3：发行说明和资源
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET 更改源 SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 连接器](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 资源提供程序](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [批量执行工具 - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量执行程序 - Java](sql-api-sdk-bulk-executor-java.md)

开发人员可以通过适用于 Core (SQL) 的 Spring Data Azure Cosmos DB 版本 3 在 Spring 应用程序中使用 Azure Cosmos DB。 Spring Data Azure Cosmos DB 公开 Spring Data 接口，以便操作数据库和集合、使用文档和发出查询。 同一 Maven 项目中同时支持 Sync 和 Async (Reactive) API。 

Spring Data Azure Cosmos DB 依赖于 Spring Data 框架。 Azure Cosmos DB SDK 团队发布了适用于 Spring Data 2.2 和 2.3 的 Maven 项目。

[Spring Framework](https://spring.io/projects/spring-framework) 是一种简化 Java 应用程序开发的编程和配置模型。 Spring 使用依赖项注入来简化应用程序的“管道”。 由于 Spring 使得生成和测试应用程序变得更加简单，因此许多开发人员都喜欢 Spring。 [Spring Boot](https://spring.io/projects/spring-boot) 重视 Web 应用程序和微服务的开发，扩展了这种管道处理。 [Spring Data](https://spring.io/projects/spring-data) 是一种编程模型和框架，用于从 Spring 或 Spring Boot 应用程序的上下文中访问数据存储（如 Azure Cosmos DB）。 

可在 [Azure Spring Cloud](https://www.azure.cn/home/features/spring-cloud/) 应用程序中使用 Spring Data Azure Cosmos DB。

<!--MOONCAKE CORRECT ON THE LINK-->

> [!IMPORTANT]  
> 这些发行说明适用于 Spring Data Azure Cosmos DB 的版本 3。 可以[在此处找到版本 2 的发行说明](sql-api-sdk-java-spring-v2.md)。 
>
> Spring Data Azure Cosmos DB 仅支持 SQL API。
>
> 请参阅以下文章，了解其他 Azure Cosmos DB API 上的 Spring Data：
> * [将适用于 Apache Cassandra 的 Spring Data 用于 Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [将 Spring Data MongoDB 用于 Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [将 Spring Data Gremlin 用于 Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

<!--MOONCAKE CORRECT ON THE LINK-->

## <a name="start-here"></a>从此处开始

# <a name="explore"></a>[探索](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

#### <a name="these-tabs-contain-basic-spring-data-azure-cosmos-db-samples"></a>这些选项卡包含基本的 Spring Data Azure Cosmos DB 示例。

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>配置依赖项

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest</version>
  </dependency>
  ```

# <a name="connect"></a>[“连接”](#tab/connect)

### <a name="connect"></a>连接

指定 Azure Cosmos DB 帐户和容器详细信息。 Spring Data Azure Cosmos DB 会自动创建客户端并连接到容器。

[application.properties](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-cosmos-java-getting-started/src/main/resources/application.properties)：
```
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

# <a name="doc-ops"></a>[文档操作](#tab/docs)

### <a name="document-operations"></a>文档操作

---

## <a name="resources"></a>资源

* **参与编写 SDK**：[GitHub 上的 Spring Data Azure Cosmos DB 存储库](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos)

* **教程**：[GitHub 上的 Spring Data Azure Cosmos DB 教程](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) 

## <a name="release-history"></a>发布历史记录

## <a name="320-beta1-unreleased"></a>3.2.0-beta.1（未发布）


### <a name="310-2020-10-21"></a>3.1.0 (2020-10-21)
#### <a name="new-features"></a>新功能
* 添加了对 `ARRAY_CONTAINS` `CriteriaType` 的支持。
* 已将 azure-cosmos 版本更新为 4.7.1。
#### <a name="key-bug-fixes"></a>关键 Bug 修复
* 修复了带批注的查询不选取带批注的容器名称的问题。

### <a name="300-2020-09-30"></a>3.0.0 (2020-09-30)
#### <a name="new-features"></a>新功能
* 已将 azure-cosmos 依赖项更新为 `4.6.0`

## <a name="300-beta2-2020-09-17"></a>3.0.0-beta.2 (2020-09-17)
#### <a name="new-features"></a>新功能
* 已将项目 ID 更新为 `azure-spring-data-cosmos`。
* 已将 azure-cosmos 依赖项更新为 `4.5.0`。
* `Query Annotation` 支持本机查询。
* 支持 Java 11。
* 通过在 `@Container` 批注中公开 `partitionKeyPath` 字段，添加了对嵌套分区键的支持。
* 添加了对 `limit` 查询类型的支持，允许在定义存储库 API 时使用 `top` 和 `first`。
#### <a name="key-bug-fixes"></a>关键 Bug 修复
* 修复了与 `@GeneratedValue` 批注一起使用时的嵌套分区键 bug。

### <a name="300-beta1-2020-08-17"></a>3.0.0-beta.1 (2020-08-17)
#### <a name="new-features"></a>新功能
* 已将组 ID 更新为 `com.azure`。
* 已将项目 ID 更新为 `azure-spring-data-cosmos-core`。
* azure-cosmos SDK 依赖项更新为 `4.3.2-beta.2`。
* 支持审核实体 - 自动管理 createdBy、createdDate、lastModifiedBy 和 lastModifiedDate 注释字段。
* `@GeneratedValue` 批注支持为 `String` 类型的 ID 字段自动生成 ID。
* 多数据库配置支持具有多个数据库的单个 cosmos 帐户和具有多个数据库的多个 cosmos 帐户。
* 支持任何字符串字段上的 `@Version` 注释。
* 同步 API 返回类型更新为 `Iterable` 类型，而非 `List`。
* 将 `CosmosClientBuilder` 从 Cosmos SDK 作为 Spring Bean 公开给 `@Configuration` 类。
* 更新了 `CosmosConfig`，以包含查询指标和响应诊断处理器实现。
* 支持为单个结果查询返回 `Optional` 数据类型。
#### <a name="renames"></a>重命名
* `CosmosDbFactory` 重命名为 `CosmosFactory`。
* `CosmosDBConfig` 重命名为 `CosmosConfig`。
* `CosmosDBAccessException` 重命名为 `CosmosAccessException`。
* `Document` 注释重命名为 `Container` 注释。
* `DocumentIndexingPolicy` 注释重命名为 `CosmosIndexingPolicy` 注释。
* `DocumentQuery` 重命名为 `CosmosQuery`。
* application.properties 标志 `populateQueryMetrics` 重命名为 `queryMetricsEnabled`。
#### <a name="key-bug-fixes"></a>关键 Bug 修复
* 将诊断日志记录任务调度给 `Parallel` 线程，以避免阻塞 Netty I/O 线程。
* 修复了对删除操作的乐观锁定。
* 修复了对 `IN` 子句查询进行转义时出现的问题。
* 通过允许为 `@Id` 使用 `long` 数据类型修复了问题。
* 通过允许将 `boolean`、`long`、`int`、`double` 作为 `@PartitionKey` 注释的数据类型修复了问题。
* 为忽略大小写查询修复了 `IgnoreCase` & `AllIgnoreCase` 关键字。
* 删除了自动创建容器时的默认请求单位值 4000。

## <a name="faq"></a>常见问题解答

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>后续步骤

了解有关 [Azure Cosmos DB](https://www.azure.cn/home/features/cosmos-db/) 的详细信息。

了解有关 [Spring Framework](https://spring.io/projects/spring-framework) 的详细信息。

详细了解有关 [Spring Boot](https://spring.io/projects/spring-boot) 的详细信息。

详细了解有关 [Spring Data](https://spring.io/projects/spring-data) 的详细信息。

<!-- Update_Description: update meta properties, wording update, update link -->
<!--NEW.date: 09/28/2020-->