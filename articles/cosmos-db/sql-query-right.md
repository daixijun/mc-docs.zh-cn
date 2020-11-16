---
title: Azure Cosmos DB 查询语言中的 RIGHT
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 RIGHT。
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 03/03/2020
author: rockboyfor
ms.date: 11/16/2020
ms.author: v-yeche
ms.custom: query-reference
ms.openlocfilehash: 734f9a533c182d292cdc0524b19957c7056e01e9
ms.sourcegitcommit: 5f07189f06a559d5617771e586d129c10276539e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94551935"
---
# <a name="right-azure-cosmos-db"></a>RIGHT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 返回具有指定字符数的字符串的右侧部分。  

## <a name="syntax"></a>语法

```sql
RIGHT(<str_expr>, <num_expr>)  
```  

## <a name="arguments"></a>参数

*str_expr*  
  要从中提取字符的字符串表达式。  

*num_expr*  
  是指定字符数的数值表达式。  

## <a name="return-types"></a>返回类型

  返回一个字符串表达式。  

## <a name="examples"></a>示例

  以下示例根据不同的长度值返回“abc”的右侧部分。  

```sql
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  

 下面是结果集。  

```json
[{"r1": "c", "r2": "bc"}]  
```  

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)

<!-- Update_Description: update meta properties, wording update, update link -->