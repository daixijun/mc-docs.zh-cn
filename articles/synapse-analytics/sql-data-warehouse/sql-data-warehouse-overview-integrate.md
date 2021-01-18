---
title: 生成集成的解决方案
description: 与 Azure Synapse Analytics 中的专用 SQL 池（之前称为 SQL DW）集成的解决方案工具和合作伙伴。
services: synapse-analytics
author: WenJason
manager: digimobile
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
origin.date: 04/17/2018
ms.date: 01/11/2021
ms.author: v-jay
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 99d2a5c51f8df6b4e45c849231406db0a52a07d2
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98022979"
---
# <a name="integrate-other-services-with-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>将其他服务与 Azure Synapse Analytics 中的专用 SQL 池（之前称为 SQL DW）集成。

借助 Azure Synapse Analytics 中的专用 SQL 池（之前称为 SQL DW）功能，用户能够与 Azure 中的许多其他服务集成。 专用 SQL 池可以利用一些其他服务，其中包括：

* Azure 数据工厂
* Azure 流分析

## <a name="azure-data-factory"></a>Azure 数据工厂

Azure 数据工厂为用户提供一个托管平台，用于创建复杂的提取和加载管道。 专用 SQL 池（之前称为 SQL DW）与 Azure 数据工厂的集成包括：

* **存储过程**：协调存储过程的执行。
* **复制**：使用 ADF 将数据移动到专用 SQL 池（之前称为 SQL DW）。 实际上，此操作可以使用 ADF 标准数据移动机制或封面下的 PolyBase。

有关详细信息，请参阅[与 Azure 数据工厂集成](../../data-factory/load-azure-sql-data-warehouse.md?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。

## <a name="azure-stream-analytics"></a>Azure 流分析

Azure 流分析是复杂、完全托管的基础结构，用于处理和使用从 Azure 事件中心生成的事件数据。  通过与专用 SQL 池（之前称为 SQL DW）集成，可以有效地处理流数据，并将其与关系数据一起存储以实现更深入、更高级的分析。  

* **作业输出：** 将流分析作业的输出直接发送到专用 SQL 池（之前称为 SQL DW）。

有关详细信息，请参阅[与 Azure 流分析集成](sql-data-warehouse-integrate-azure-stream-analytics.md)。
