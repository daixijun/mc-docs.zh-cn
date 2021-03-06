---
title: Azure Cosmos DB 查询语言中的 AVG
description: 了解 Azure Cosmos DB 中的 Average (AVG) SQL 系统函数。
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
origin.date: 12/02/2020
author: rockboyfor
ms.date: 01/18/2021
ms.testscope: yes
ms.testdate: 01/18/2021
ms.author: v-yeche
ms.custom: query-reference
ms.openlocfilehash: 3df1265bdf3f1f685a4c181a2233acdaeb97b926
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98231136"
---
<!--Verified successfully-->
# <a name="avg-azure-cosmos-db"></a>AVG (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

此聚合函数返回表达式中值的平均值。

## <a name="syntax"></a>语法

```sql
AVG(<numeric_expr>)  
```  

## <a name="arguments"></a>参数

*numeric_expr*  

是一个数值表达式。  

## <a name="return-types"></a>返回类型

返回一个数值表达式。  

## <a name="examples"></a>示例

以下示例返回 `propertyA` 的平均值：

```sql
SELECT AVG(c.propertyA)
FROM c
```  

## <a name="remarks"></a>备注

此系统函数将从[范围索引](index-policy.md#includeexclude-strategy)中获益。 如果 `AVG` 中的任何参数是字符串、布尔值或 null，则整个聚合系统函数将返回 `undefined`。 如果任何参数具有 `undefined` 值，则不会影响 `AVG` 计算。

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB 中的数学函数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB 中的系统函数](sql-query-system-functions.md)
- [Azure Cosmos DB 中的聚合函数](sql-query-aggregate-functions.md)

<!-- Update_Description: new article about sql query aggregate avg -->
<!--NEW.date: 01/18/2021-->