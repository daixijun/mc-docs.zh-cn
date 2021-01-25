---
title: Azure Cosmos DB 查询语言中的 COUNT
description: 了解 Azure Cosmos DB 中的 Count (COUNT) SQL 系统函数。
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
ms.openlocfilehash: c4ab3fc34f3328ca8ee681cd7d8f4a403eae609d
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98231135"
---
<!--Verified successfully-->
# <a name="count-azure-cosmos-db"></a>COUNT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

此系统函数返回表达式中值的计数。

## <a name="syntax"></a>语法

```sql
COUNT(<scalar_expr>)  
```  

## <a name="arguments"></a>参数

scalar_expr  

是任意标量表达式

## <a name="return-types"></a>返回类型

返回一个数值表达式。  

## <a name="examples"></a>示例

以下示例返回容器中项的总计数：

```sql
SELECT COUNT(1)
FROM c
``` 
COUNT 可以采用任何标量表达式作为输入。 以下查询将生成等效的结果：

```sql
SELECT COUNT(2)
FROM c
```

## <a name="remarks"></a>备注

此系统函数将受益于查询筛选器中任何属性的[范围索引](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB 中的数学函数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB 中的系统函数](sql-query-system-functions.md)
- [Azure Cosmos DB 中的聚合函数](sql-query-aggregate-functions.md)

<!-- Update_Description: new article about sql query aggregate count -->
<!--NEW.date: 01/18/2021-->