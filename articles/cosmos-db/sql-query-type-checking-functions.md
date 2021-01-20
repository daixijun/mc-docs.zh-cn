---
title: Azure Cosmos DB 查询语言中的类型检查函数
description: 了解 Azure Cosmos DB 中的类型检查 SQL 系统函数。
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
origin.date: 09/13/2019
author: rockboyfor
ms.date: 01/18/2021
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.custom: query-reference
ms.openlocfilehash: de5ceb915ae2ccaa4a22e5a1dee3c33594470b68
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98230957"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>类型检查函数 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

使用类型检查函数可以检查 SQL 查询中表达式的类型。 当项中的属性可变或未知时，可以使用类型检查函数即时确定这些属性的类型。 

## <a name="functions"></a>函数

下表列出了支持的内置类型检查函数：

以下函数支持对输入值进行类型检查，并且每个都会返回一个布尔值。  

* [IS_ARRAY](sql-query-is-array.md)
* [IS_BOOL](sql-query-is-bool.md)
* [IS_DEFINED](sql-query-is-defined.md)
* [IS_NULL](sql-query-is-null.md)
* [IS_NUMBER](sql-query-is-number.md)
* [IS_OBJECT](sql-query-is-object.md)
* [IS_PRIMITIVE](sql-query-is-primitive.md)
* [IS_STRING](sql-query-is-string.md)

## <a name="next-steps"></a>后续步骤

- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
- [用户定义的函数](sql-query-udfs.md)
- [聚合](sql-query-aggregate-functions.md)

<!-- Update_Description: update meta properties, wording update, update link -->