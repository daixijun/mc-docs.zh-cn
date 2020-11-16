---
title: 表 API 的 Azure Cosmos DB 多区域分发教程
description: 了解多区域分布如何在 Azure Cosmos DB 表 API 帐户中工作以及如何配置首选区域列表
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
origin.date: 01/30/2020
author: rockboyfor
ms.date: 11/16/2020
ms.author: v-yeche
ms.reviewer: sngun
ms.openlocfilehash: 76aac680aafc8fd29024f818265a9d684d70bed0
ms.sourcegitcommit: 5f07189f06a559d5617771e586d129c10276539e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94551717"
---
<!--Verify sucessfully-->
# <a name="set-up-azure-cosmos-db-multiple-region-distribution-using-the-table-api"></a>使用表 API 设置 Azure Cosmos DB 多区域分发
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

本文涵盖以下任务： 

> [!div class="checklist"]
> * 使用 Azure 门户配置多区域分发
> * 使用[表 API](table-introduction.md) 配置多区域分发

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>使用表 API 连接到首选区域

为了利用[多区域分发](distribute-data-globally.md)，客户端应用程序应当指定其应用程序在其中运行的当前位置。 这是通过设置 `CosmosExecutorConfiguration.CurrentRegion` 属性实现的。 `CurrentRegion` 属性应当包含单个位置。 每个客户端实例都可以指定其自己的区域，以实现低延迟读取。 区域必须使用其[显示名称](https://docs.microsoft.com/previous-versions/azure/reference/gg441293(v=azure.100))命名，例如“中国北部”。 

Azure Cosmos DB 表 API SDK 将基于帐户配置和当前的区域可用性自动选取要与之通信的最佳终结点。 它优先选择最靠近的区域来使客户端实现更低的延迟。 在设置当前的 `CurrentRegion` 属性后，读取和写入请求将如下所述进行定向：

* **读取请求：** 所有读取请求都将发送到所配置的 `CurrentRegion`。 SDK 会基于邻近性自动选择回退异地复制区域以实现高可用性。

* **写入请求：** SDK 会自动将所有写入请求发送到当前写入区域。 在具有多区域写入功能的帐户中，当前区域也将为写入请求提供服务。 SDK 会基于邻近性自动选择回退异地复制区域以实现高可用性。

如果未指定 `CurrentRegion` 属性，则 SDK 会将当前写入区域用于所有操作。

例如，如果某个 Azure Cosmos 帐户位于“中国北部”和“中国东部”区域中。 如果“中国北部”是写入区域，并且应用程序位于“中国东部”。 如果未配置 CurrentRegion 属性，则所有读取和写入请求始终会定向到“中国北部”区域。 如果配置了 CurrentRegion 属性，则所有读取请求都将在“中国东部”区域中接受服务。

## <a name="next-steps"></a>后续步骤

在本教程中，已完成以下内容：

> [!div class="checklist"]
> * 使用 Azure 门户配置多区域分发
> * 使用 Azure Cosmos DB 表 API 配置多区域分发

<!--Update_Description: update link -->
