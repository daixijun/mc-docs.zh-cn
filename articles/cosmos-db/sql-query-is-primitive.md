---
title: Azure Cosmos DB 查询语言中的 IS_PRIMITIVE
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 IS_PRIMITIVE。
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
origin.date: 09/13/2019
author: rockboyfor
ms.date: 12/14/2020
ms.author: v-yeche
ms.custom: query-reference
ms.openlocfilehash: f9e688549554134e4d3ef253e5bdb80a23a6fbc0
ms.sourcegitcommit: a8afac9982deafcf0652c63fe1615ba0ef1877be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96850709"
---
# <a name="is_primitive-azure-cosmos-db"></a>IS_PRIMITIVE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 返回一个布尔值，指示指定表达式的类型是否为一个（字符串、布尔、数值或 null）。  

## <a name="syntax"></a>语法

```sql
IS_PRIMITIVE(<expr>)  
```  

## <a name="arguments"></a>参数

*expr*  
  是任何表达式。  

## <a name="return-types"></a>返回类型

  返回一个布尔表达式。  

## <a name="examples"></a>示例

  以下示例使用 `IS_PRIMITIVE` 函数检查 JSON 布尔、数字、字符串、null、对象、数组和 undefined 类型的对象。  

```sql
SELECT   
    IS_PRIMITIVE(true) AS isPrim1,   
    IS_PRIMITIVE(1) AS isPrim2,  
    IS_PRIMITIVE("value") AS isPrim3,   
    IS_PRIMITIVE(null) AS isPrim4,  
    IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
    IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
    IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  

 下面是结果集。  

```json
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  

## <a name="remarks"></a>备注

此系统函数将从[范围索引](index-policy.md#includeexclude-strategy)中获益。

## <a name="next-steps"></a>后续步骤

- [类型检查函数 Azure Cosmos DB](sql-query-type-checking-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)

<!-- Update_Description: update meta properties, wording update, update link -->