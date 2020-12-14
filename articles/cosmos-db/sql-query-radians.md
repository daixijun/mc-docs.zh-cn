---
title: Azure Cosmos DB 查询语言中的 RADIANS
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 RADIANS。
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
origin.date: 09/13/2019
author: rockboyfor
ms.date: 12/14/2020
ms.author: v-yeche
ms.custom: query-reference
ms.openlocfilehash: a2da37cf69e83341d7b3b9ad9edece877a6dd98d
ms.sourcegitcommit: a8afac9982deafcf0652c63fe1615ba0ef1877be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96850673"
---
# <a name="radians-azure-cosmos-db"></a>RADIANS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 返回输入的数值表达式（度）的弧度。  

## <a name="syntax"></a>语法

```sql
RADIANS (<numeric_expr>)  
```  

## <a name="arguments"></a>参数

*numeric_expr*  
  是一个数值表达式。  

## <a name="return-types"></a>返回类型

  返回一个数值表达式。  

## <a name="examples"></a>示例

  以下示例采用几个角度作为输入并返回其对应的弧度值。  

```sql
SELECT RADIANS(-45.01) AS r1, RADIANS(-181.01) AS r2, RADIANS(0) AS r3, RADIANS(0.1472738) AS r4, RADIANS(197.1099392) AS r5  
```  

  下面是结果集。  

```json
[{  
    "r1": -0.7855726963226477,  
    "r2": -3.1592204790349356,  
    "r3": 0,  
    "r4": 0.0025704127119236249,  
    "r5": 3.4402174274458375  
}]  
```  

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)

<!-- Update_Description: update meta properties, wording update, update link -->