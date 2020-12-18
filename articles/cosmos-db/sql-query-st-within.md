---
title: Azure Cosmos DB 查询语言中的 ST_WITHIN
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 ST_WITHIN。
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
origin.date: 11/23/2020
author: rockboyfor
ms.date: 12/14/2020
ms.author: v-yeche
ms.custom: query-reference
ms.openlocfilehash: 28eedbe7a7ee526da3b10c8c6d5544ef534ff14f
ms.sourcegitcommit: a8afac9982deafcf0652c63fe1615ba0ef1877be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96850526"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 返回一个布尔表达式，指示在第一个参数中指定的 GeoJSON 对象（Point、Polygon、MultiPolygon 或 LineString）是否位于第二个参数中的 GeoJSON（Point、Polygon、MultiPolygon 或 LineString）内。  

## <a name="syntax"></a>语法

```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  

## <a name="arguments"></a>参数

*spatial_expr*  
  是 GeoJSON 点、多边形或 LineString 对象表达式。  

## <a name="return-types"></a>返回类型

  返回一个布尔值。  

## <a name="examples"></a>示例

  以下示例演示了如何使用 `ST_WITHIN` 查找某个多边形内的所有家族文档。  

```sql
SELECT f.id
FROM Families f
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  

 下面是结果集。  

```json
[{ "id": "WakefieldFamily" }]  
```  

## <a name="remarks"></a>备注

此系统函数将从[地理空间索引](index-policy.md#spatial-indexes)中获益。

## <a name="next-steps"></a>后续步骤

- [空间函数 Azure Cosmos DB](sql-query-spatial-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)

<!-- Update_Description: update meta properties, wording update, update link -->