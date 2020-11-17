---
title: Azure Cosmos DB 查询语言中的 LEFT
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 LEFT。
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 09/13/2019
author: rockboyfor
ms.date: 11/16/2020
ms.author: v-yeche
ms.custom: query-reference
ms.openlocfilehash: 206e30345a63bfdcf276c6ed8b835d2889f7262b
ms.sourcegitcommit: 5f07189f06a559d5617771e586d129c10276539e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94552511"
---
# <a name="left-azure-cosmos-db"></a>LEFT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 返回具有指定字符数的字符串的左侧部分。  

## <a name="syntax"></a>语法

```sql
LEFT(<str_expr>, <num_expr>)  
```  

## <a name="arguments"></a>参数

*str_expr*  
  要从中提取字符的字符串表达式。  

*num_expr*  
  是指定字符数的数值表达式。  

## <a name="return-types"></a>返回类型

  返回一个字符串表达式。  

## <a name="examples"></a>示例

  以下示例根据不同的长度值返回“abc”的左侧部分。  

```sql
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  

 下面是结果集。  

```json
[{"l1": "a", "l2": "ab"}]  
```  

## <a name="remarks"></a>备注

此系统函数将从[范围索引](index-policy.md#includeexclude-strategy)中获益。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)

<!-- Update_Description: update meta properties, wording update, update link -->