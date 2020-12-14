---
title: Azure Cosmos DB 查询语言中的 UPPER
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 UPPER。
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
origin.date: 03/04/2020
author: rockboyfor
ms.date: 12/14/2020
ms.author: v-yeche
ms.custom: query-reference
ms.openlocfilehash: fabd49ec3d40dff8dacc83d9f88fbbf28c2e5cd2
ms.sourcegitcommit: a8afac9982deafcf0652c63fe1615ba0ef1877be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96850451"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 返回在将小写字符数据转换为大写后的字符串表达式。  

UPPER 系统函数不使用索引。 如果计划频繁进行不区分大小写的比较，则 UPPER 系统函数可能会消耗大量 RU。 如果是这种情况，可以在插入时将大小写规范化，而不是每次都使用 UPPER 系统函数将数据规范化以进行比较。 于是，诸如 SELECT * FROM c WHERE UPPER(c.name) = 'BOB' 的查询只需变成 SELECT * FROM c WHERE c.name = 'BOB'。

## <a name="syntax"></a>语法

```sql
UPPER(<str_expr>)  
```  

## <a name="arguments"></a>参数

*str_expr*  
  是一个字符串表达式。  

## <a name="return-types"></a>返回类型

返回一个字符串表达式。  

## <a name="examples"></a>示例

以下示例演示如何在查询中使用 `UPPER`  

```sql
SELECT UPPER("Abc") AS upper  
```  

 下面是结果集。  

```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)

<!-- Update_Description: update meta properties, wording update, update link -->