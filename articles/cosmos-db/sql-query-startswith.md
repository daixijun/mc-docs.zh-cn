---
title: Azure Cosmos DB 查询语言中的 StartsWith
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 STARTSWITH。
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
origin.date: 05/20/2020
author: rockboyfor
ms.date: 12/14/2020
ms.author: v-yeche
ms.custom: query-reference
ms.openlocfilehash: fbea6ad04bfc07148540328ee50f01f7328876e8
ms.sourcegitcommit: a8afac9982deafcf0652c63fe1615ba0ef1877be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96850520"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式开头。  

## <a name="syntax"></a>语法

```sql
STARTSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  

## <a name="arguments"></a>参数

str_expr1  

   一个字符串表达式。

str_expr2  

   要与 str_expr1 的开头进行比较的字符串表达式。

*bool_expr* 
   
   用于忽略大小写的可选值。 如果设置为 true，则 STARTSWITH 将执行不区分大小写的搜索。 如果未指定，则此值为 false。

## <a name="return-types"></a>返回类型

返回一个布尔表达式。  

## <a name="examples"></a>示例

以下示例检查了字符串“abc”是否以“b”和“A”开头。  

```sql
SELECT STARTSWITH("abc", "b", false) AS s1, STARTSWITH("abc", "A", false) AS s2, STARTSWITH("abc", "A", true) AS s3
```  

 下面是结果集：  

```json
[
    {
        "s1": false,
        "s2": false,
        "s3": true
    }
]
```  

## <a name="remarks"></a>备注

此系统函数将从[范围索引](index-policy.md#includeexclude-strategy)中获益。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)

<!-- Update_Description: update meta properties, wording update, update link -->