---
title: Azure Cosmos DB Core (SQL API) 的 Azure 资源管理器模板
description: 使用 Azure 资源管理器模板创建和配置 Azure Cosmos DB。
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 10/14/2020
author: rockboyfor
ms.date: 11/09/2020
ms.testscope: yes|no
ms.testdate: 11/09/2020null
ms.author: v-yeche
ms.openlocfilehash: aa00aa89bcfa8372456d70124f6ab4f8b6e50049
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329148"
---
<!--Verified successfully-->
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>用于 Azure Cosmos DB 的 Azure 资源管理器模板
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

本文仅显示适用于 Core (SQL) API 帐户的 Azure 资源管理器模板示例。 你还可以查找 [Cassandra](templates-samples-cassandra.md)、[Gremlin](templates-samples-gremlin.md)、[MongoDB](templates-samples-mongodb.md) 和[表](templates-samples-table.md) API 的模板示例。

## <a name="core-sql-api"></a>Core (SQL) API

|**模板**|**说明**|
|---|---|
|[创建具有自动缩放吞吐量的 Azure Cosmos 帐户、数据库和容器](manage-with-templates.md#create-autoscale) | 此模板在两个区域（具有自动缩放吞吐量的数据库和容器）中创建一个 Core (SQL) API 帐户。 |
|[创建具有分析存储的 Azure Cosmos 帐户、数据库和容器](manage-with-templates.md#create-analytical-store) | 此模板在一个区域中创建一个 Core (SQL) API 帐户，其中包含配置了启用分析 TTL 的容器和手动或自动缩放吞吐量选项。 |
|[创建具有标准（手动）吞吐量的 Azure Cosmos 帐户、数据库和容器](manage-with-templates.md#create-manual) | 此模板在两个区域（具有标准吞吐量的数据库和容器）中创建一个 Core (SQL) API 帐户。 |
|[创建包含存储过程、触发器和 UDF 的 Azure Cosmos 帐户、数据库和容器](manage-with-templates.md#create-sproc) | 此模板在两个区域（具有存储过程、触发器和容器的 UDF）中创建了一个 Core (SQL) API 帐户。 |
|[为现有的 Azure Cosmos 帐户创建专用终结点](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  此模板为现有虚拟网络中的现有 Azure Cosmos Core (SQL) API 帐户创建专用终结点。 |
|[创建免费层 Azure Cosmos 帐户](manage-with-templates.md#free-tier) |  此模板在免费层上创建 Azure Cosmos DB Core (SQL) API 帐户。 |

<!--Not Available on [Azure Resource Manager reference for Azure Cosmos DB](https://docs.microsoft.com/azure/templates/microsoft.documentdb/allversions)-->

<!-- Update_Description: new article about templates samples sql -->
<!--NEW.date: 11/09/2020-->