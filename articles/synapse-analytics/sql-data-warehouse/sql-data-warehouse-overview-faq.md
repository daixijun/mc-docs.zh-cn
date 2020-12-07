---
title: Azure Synapse Analytics（前称为 SQL 数据仓库）常见问题解答
description: 本文列出了客户和开发人员提出的有关 Azure Synapse Analytics（前称为 SQL 数据仓库）的常见问题的解答。
services: synapse-analytics
author: WenJason
manager: digimobile
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
origin.date: 11/04/2019
ms.date: 11/30/2020
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: c71830717aa517a26d0e0164aa6438d9144c1949
ms.sourcegitcommit: dabbf66e4507a4a771f149d9f66fbdec6044dfbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96152942"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Azure Synapse Analytics（前称为 SQL 数据仓库）常见问题解答

## <a name="general"></a>常规

问： 什么是 Azure Synapse？

A. Azure Synapse 是一种分析服务，它将数据仓库和大数据分析结合在一起。 Azure Synapse 将这两个领域结合在一起，提供统一的体验来引入、准备、管理和处理数据。 有关详细信息，请参阅[什么是 Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)。

问： Azure SQL 数据仓库有什么变化？

A. Azure Synapse 是 Azure SQL 数据仓库的演进版。 我们将同一个行业领先的数据仓库提升到了一个全新的性能和功能级别。 你可以继续使用 Azure Synapse 在生产环境中运行现有的数据仓库工作负荷。 有关详细信息，请参阅[什么是 Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)。

问： 什么是专用 SQL 池？

A. 专用 SQL 池是指 Azure Synapse 中正式发布的企业数据仓库功能。 有关详细信息，请参阅[什么是 Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)。

问： Azure Synapse 如何入门？

A. 可以从一个 [Azure 试用帐户](https://www.azure.cn/zh-cn/pricing/1rmb-trial-full/?form-type=identityauth)着手。 

问： Azure Synapse 提供哪些功能来确保数据安全性？

A. Azure Synapse 提供多种解决方案来保护数据，例如 TDE 和审核。 有关详细信息，请参阅[安全性](sql-data-warehouse-overview-manage-security.md)。

问： 从何处可以查明 Azure Synapse 符合哪些法律或企业标准？

A. 请访问 [Azure 符合性](https://www.trustcenter.cn/zh-cn/cloudservices/azure.html)页，按产品（如 SOC 和 ISO）了解各种合规性产品。 

问： 是否可以连接 Power BI？

A. 能！ 尽管 Power BI 支持使用 Azure Synapse 进行直接查询，但不适合大量用户或实时数据。 若要进一步优化 Power BI 性能，请考虑在 Azure Analysis Services 或 Analysis Service IaaS 的顶层使用 Power BI。

问： 什么是专用 SQL 池容量限制？

A. 请参阅当前[容量限制](sql-data-warehouse-service-capacity-limits.md)页。

问： 为什么缩放/暂停/恢复会花费很长时间？

A. 多种因素可能会影响计算管理操作的时间。 长时间运行的操作的常见例子是事务回退。 缩放或暂停操作启动时，会阻止所有传入会话和查询。 为了使系统处于稳定状态，必须在操作开始前回退事务。 事务数量越多，其日志大小越大，使系统恢复到稳定状态的操作耗时越久。

## <a name="user-support"></a>用户支持

问： 该如何操作？

A. 若要获得有关使用 Azure Synapse 开发的帮助，可在 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw) 页上提问。

## <a name="sql-languagefeature-support"></a>SQL 语言/功能支持

问： 支持哪些数据类型？

A. 请参阅[数据类型](sql-data-warehouse-tables-data-types.md)。

问： 支持哪些表功能？

A. 支持许多功能。 不支持的功能可在[不支持的表功能](sql-data-warehouse-tables-data-types.md)中找到。

## <a name="tooling-and-administration"></a>工具和管理

问： 专用 SQL 池是否支持 REST API？

A. 是的。 专用 SQL 池还提供了可与 SQL 数据库配合使用的大多数 REST 功能。 可以在 REST 文档页或[数据库](https://docs.microsoft.com/rest/api/sql/databases?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)中找到 API 信息。

## <a name="loading"></a>加载

问： 支持哪些客户端驱动程序？

A. 可在[连接字符串](sql-data-warehouse-connection-strings.md)页上找到专用 SQL 池的驱动程序支持

问：PolyBase 支持哪些文件格式？

答：Orc、RC、Parquet 和带分隔符的平面文本

问：使用 PolyBase 可以连接到哪些数据源？

答：[Azure 存储 Blob](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

问：连接 Azure 存储 Blob 或 ADLS 时能否进行计算下推？

答：不能，PolyBase 仅与存储组件交互。

问：能否连接到 HDI？

答：HDI 可使用 ADLS 或 WASB 作为 HDFS 层。 如果将两者中任意一种作为 HDFS 层，则可将该数据加载到专用 SQL 池中。 但是，无法生成 HDI 实例的下推计算。

## <a name="next-steps"></a>后续步骤

有关 Azure Synapse 的综合性详细信息，请参阅[概述](sql-data-warehouse-overview-faq.md)页。
