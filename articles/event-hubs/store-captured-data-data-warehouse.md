---
title: 教程：将事件数据迁移到 Azure Synapse Analytics - Azure 事件中心
description: 介绍如何使用 Azure 事件网格和 Functions 将事件中心捕获的数据迁移到 Azure Synapse Analytics。
services: event-hubs
origin.date: 06/23/2020
ms.date: 01/05/2021
ms.author: v-tawe
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 39a6497f6f1066619d82ee0f694801edab6810cc
ms.sourcegitcommit: ff20289adb80a6ab45e15fa5e196ff7af7e1c6b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874852"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-azure-synapse-analytics-using-event-grid-and-azure-functions"></a>教程：使用事件网格和 Azure Functions 将捕获的事件中心数据迁移到 Azure Synapse Analytics
Azure 事件中心[捕获](./event-hubs-capture-overview.md)用于自动捕获 Azure Blob 存储或 Azure Data Lake Storage 中事件中心的流式处理数据。 本教程介绍如何使用[事件网格](../event-grid/overview.md)触发的 Azure 函数将捕获的事件中心数据从存储迁移到 Azure Synapse Analytics。

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>后续步骤 
可以将强大的数据可视化工具与数据仓库配合使用，以便获取可行的见解。

本文介绍如何[将 Power BI 与 Azure Synapse Analytics 配合使用](https://docs.microsoft.com/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)