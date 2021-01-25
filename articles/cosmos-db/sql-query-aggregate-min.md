---
title: Azure Cosmos DB 查询语言中的 MIN
description: 了解 Azure Cosmos DB 中的 Min (MIN) SQL 系统函数。
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
ms.openlocfilehash: e90c47a309daba10aae13d1414592515b3807897
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98231152"
---
<!--Verified successfully-->
# <a name="min-azure-cosmos-db"></a>MIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

此聚合函数返回表达式中值的最小值。

## <a name="syntax"></a>语法

```sql
MIN(<scalar_expr>)  
```  

## <a name="arguments"></a>参数

scalar_expr  

它是一个标量表达式。 

## <a name="return-types"></a>返回类型

返回标量表达式。  

## <a name="examples"></a>示例

以下示例返回 `propertyA` 的最小值：

```sql
SELECT MIN(c.propertyA)
FROM c
```  

## <a name="remarks"></a>备注

此系统函数将从[范围索引](index-policy.md#includeexclude-strategy)中获益。 `MIN` 中的参数可以是数字、字符串、布尔值或 null。 任何未定义的值将被忽略。

比较不同类型的数据时，使用以下优先级顺序（升序）：

- Null
- boolean
- number
- string

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB 中的数学函数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB 中的系统函数](sql-query-system-functions.md)
- [Azure Cosmos DB 中的聚合函数](sql-query-aggregate-functions.md)

<!-- Update_Description: new article about sql query aggregate min -->
<!--NEW.date: 01/18/2021-->