---
title: 使用标签来检测查询
description: 使用标签在 Synapse SQL 池中检测查询以开发解决方案的技巧。
services: synapse-analytics
author: WenJason
manager: digimobile
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
origin.date: 04/17/2018
ms.date: 11/09/2020
ms.author: v-jay
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 260a80019cbf6944b2ab7c048ee97d94111ee397
ms.sourcegitcommit: b217474b15512b0f40b2eaae66bd3c521383d321
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375605"
---
# <a name="using-labels-to-instrument-queries-in-synapse-sql-pool"></a>在 Synapse SQL 池中使用标签来检测查询

本文介绍了使用标签在 SQL 池中检测查询以开发解决方案的技巧。

有关在开发解决方案时于 Azure Synapse Analytics 中使用标签检测查询的技巧。

## <a name="what-are-labels"></a>什么是标签？

SQL 池支持称为“查询标签”的概念。 在继续之前，让我们看一个示例：

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

最后一行将字符串“My Query Label”标记为查询。 此标记很有用，因为可以通过 DMV 查询标签。

对标签进行查询提供了一种用于定位有问题的查询并帮助查明 ELT 运行进度的机制。

良好的命名约定确实很有帮助。 例如，让标签以 PROJECT、PROCEDURE、STATEMENT 或 COMMENT 开头可在源代码管理的所有代码中唯一地标识查询。

以下查询使用动态管理视图按标签进行搜索：

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> 查询时，必须以方括号或双引号括住文字标签。 Label 是一个保留字，不将其分隔会导致错误。

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅 [开发概述](sql-data-warehouse-overview-develop.md)。
