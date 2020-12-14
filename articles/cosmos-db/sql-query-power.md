---
title: Azure Cosmos DB 查询语言中的 POWER
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 POWER。
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
origin.date: 09/13/2019
author: rockboyfor
ms.date: 12/14/2020
ms.author: v-yeche
ms.custom: query-reference
ms.openlocfilehash: ac85cb352ed985cd4f89a47233d6c70d9a393598
ms.sourcegitcommit: a8afac9982deafcf0652c63fe1615ba0ef1877be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96850675"
---
# <a name="power-azure-cosmos-db"></a>POWER (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 返回指定表达式的指定幂的值。  

## <a name="syntax"></a>语法

```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  

## <a name="arguments"></a>参数

*numeric_expr1*  
  为数值表达式。  

*numeric_expr2*  
  是要将 *numeric_expr1* 提升到的幂次。  

## <a name="return-types"></a>返回类型

  返回数值表达式。  

## <a name="examples"></a>示例

  下列示例演示一个数字的 3 次幂（数的立方）的运算。  

```sql
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  

 下面是结果集：  

```json
[{pow1: 8, pow2: 15.625}]  
```  

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)

<!-- Update_Description: update meta properties, wording update, update link -->