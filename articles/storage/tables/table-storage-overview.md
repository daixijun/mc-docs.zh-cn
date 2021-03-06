---
title: 表存储简介 - Azure 中的对象存储 | Microsoft Docs
description: 使用 Azure 表存储（一种 NoSQL 数据存储）将结构化数据存储在云中。
services: storage
ms.service: storage
author: WenJason
ms.author: v-jay
ms.devlang: dotnet
ms.topic: overview
origin.date: 01/07/2021
ms.date: 01/18/2021
ms.subservice: tables
ms.openlocfilehash: 422a1b0764dffa9c3fbb99d50d26ca5a16ec5142
ms.sourcegitcommit: f086abe8bd2770ed10a4842fa0c78b68dbcdf771
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2021
ms.locfileid: "98163208"
---
# <a name="what-is-azure-table-storage-"></a>什么是 Azure 表存储？ 

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Azure 表存储是一项用于在云中存储非关系结构化数据（也称为结构化 NoSQL 数据）的服务，通过无架构设计提供键/属性存储。 因为表存储无架构，因此可以很容易地随着应用程序需求的发展使数据适应存储。 对于许多类型的应用程序来说，访问表存储数据速度快且经济高效，在数据量相似的情况下，其成本通常比传统 SQL 要低。

可以使用表存储来存储灵活的数据集，例如 Web 应用程序的用户数据、通讯簿、设备信息，或者服务需要的其他类型的元数据。 可以在表中存储任意数量的实体，并且一个存储帐户可以包含任意数量的表，直至达到存储帐户的容量极限。

[!INCLUDE [storage-table-concepts-include](../../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>后续步骤

* [Microsoft Azure 存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md)是 Microsoft 免费提供的独立应用，适用于在 Windows、macOS 和 Linux 上以可视方式处理 Azure 存储数据。

* [.NET 中的 Azure 表存储入门](../../cosmos-db/tutorial-develop-table-dotnet.md)

* 查看表服务参考文档，了解有关可用 API 的完整详情：

    * [.NET 存储客户端库参考](https://docs.azure.cn/zh-cn/dotnet/api/overview/storage)

    * [REST API 参考](https://docs.microsoft.com/rest/api/storageservices/)