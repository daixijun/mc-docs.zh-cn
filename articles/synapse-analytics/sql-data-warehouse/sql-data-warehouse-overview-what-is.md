---
title: 什么是专用 SQL 池（之前称为 SQL DW）？
description: Azure Synapse Analytics 中的专用 SQL 池（之前称为 SQL DW）是 Azure Synapse Analytics 中的企业数据仓库功能。
services: synapse-analytics
author: WenJason
manager: digimobile
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
origin.date: 11/04/2019
ms.date: 01/11/2021
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: ab7791ce0fc3bb50b04e502ed5b44a5451e1eab7
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98023253"
---
# <a name="what-is-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>什么是 Azure Synapse Analytics 中的专用 SQL 池（之前称为 SQL DW）？

Azure Synapse Analytics 是一种分析服务，它将企业数据仓库和大数据分析结合在一起。 专用 SQL 池（之前称为 SQL DW）是指 Azure Synapse Analytics 中发布的企业数据仓库功能。

专用 SQL 池（之前称为 SQL DW）表示使用 Synapse SQL 时预配的分析资源集合。 专用 SQL 池（之前称为 SQL DW）的大小由数据仓库单位 (DWU) 决定。

创建专用 SQL 池后，可以使用简单的 [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL 查询导入大数据，然后利用分布式查询引擎的功能运行高性能分析。 对数据进行集成和分析时，专用 SQL 池（之前称为 SQL DW）将成为企业赖以获取更快、更可靠见解的唯一信息源。

## <a name="key-component-of-a-big-data-solution"></a>大数据解决方案的关键组件

数据仓库是基于云的端到端大数据解决方案的关键组件。

![数据仓库解决方案](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png)

在云数据解决方案中，可从各种源将数据引入大数据存储中。 将数据置于大数据存储中以后，Hadoop、Spark 和机器学习算法就可以准备和训练数据。 当数据可供进行复杂的分析时，专用 SQL 池就会使用 PolyBase 来查询大数据存储。 PolyBase 使用标准 T-SQL 查询将数据引入专用 SQL 池（之前称为 SQL DW）表中。

专用 SQL 池（之前称为 SQL DW）通过分列存储将数据存储到关系表中。 此格式可显著降低数据存储成本，并改进查询性能。 存储数据后，即可大规模地运行分析。 与传统数据库系统相比，数分钟的分析查询只需数秒即可完成，数天的查询只需数小时。

分析结果可以传输到世界各地的报告数据库或应用程序。 然后即可通过业务分析获得进行明智的业务决策所需的见解。

## <a name="next-steps"></a>后续步骤

- 浏览 [Azure Synapse 体系结构](massively-parallel-processing-mpp-architecture.md)
- 快速[创建专用 SQL 池](create-data-warehouse-portal.md)
- [加载示例数据](load-data-from-azure-blob-storage-using-polybase.md)
