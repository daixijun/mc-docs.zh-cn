---
title: 用于在 Azure Synapse Analytics 中开发专用 SQL 池的资源
description: Azure Synapse Analytics 中专用 SQL 池的开发概念、设计决策、建议和编码技术。
services: synapse-analytics
author: WenJason
manager: digimobile
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
origin.date: 08/29/2018
ms.date: 11/30/2020
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 188319a482102c44aaa308eafc1360878b09c3d2
ms.sourcegitcommit: dabbf66e4507a4a771f149d9f66fbdec6044dfbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96152963"
---
# <a name="design-decisions-and-coding-techniques-for-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中专用 SQL 池的设计决策和编码技术 

 在本文中，你将找到更多资源，帮助你更好地理解 Azure Synapse 中专用 SQL 池的关键设计决策、建议和编码技术。

## <a name="key-design-decisions"></a>关键设计决策

以下文章重点介绍了使用 Azure Synapse 中的专用 SQL 池功能开发分布式数据仓库的概念和设计决策：

* [连接](sql-data-warehouse-connect-overview.md)
* [并发](resource-classes-for-workload-management.md)
* [事务](sql-data-warehouse-develop-transactions.md)
* [用户定义的架构](sql-data-warehouse-develop-user-defined-schemas.md)
* [表分布](sql-data-warehouse-tables-distribute.md)
* [表索引](sql-data-warehouse-tables-index.md)
* [表分区](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [统计信息](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>开发建议和编程技术

以下文章介绍了开发专用 SQL 池的特定编码技术、技巧和建议：

* [存储过程](sql-data-warehouse-develop-stored-procedures.md)
* [标签](sql-data-warehouse-develop-label.md)
* [视图](performance-tuning-materialized-views.md)
* [临时表](sql-data-warehouse-tables-temporary.md)
* [动态 SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [循环](sql-data-warehouse-develop-loops.md)
* [group by 选项](sql-data-warehouse-develop-group-by-options.md)
* [变量赋值](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>后续步骤

有关更多参考信息，请参阅 [T-SQL 语句](sql-data-warehouse-reference-tsql-statements.md)。
