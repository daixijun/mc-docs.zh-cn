---
title: Azure Cosmos DB 查询语言中的 GetCurrentDateTime
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 GetCurrentDateTime。
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 08/18/2020
author: rockboyfor
ms.date: 11/16/2020
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.custom: query-reference
ms.openlocfilehash: 53bd55a9cc4f299707e044f63a366f0b56729661
ms.sourcegitcommit: 5f07189f06a559d5617771e586d129c10276539e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94552626"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

以 ISO 8601 字符串形式返回当前 UTC（协调世界时）日期和时间。

## <a name="syntax"></a>语法

```sql
GetCurrentDateTime ()
```

## <a name="return-types"></a>返回类型

  以 `YYYY-MM-DDThh:mm:ss.fffffffZ` 格式返回当前 UTC 日期和时间 ISO 8601 字符串值，其中：

  |格式|描述|
  |-|-|
  |YYYY|四位数的年份|
  |MM|两位数的月份（01 = 1 月，依此类推。）|
  |DD|两位数的月份日期（01 到 31）|
  |T|时间元素开头的符号|
  |hh|两位数的小时（00 到 23）|
  |MM|两位数的分钟（00 到 59）|
  |ss|两位数的秒（00 到 59）|
  |.fffffff|七位数的小数秒|
  |Z|UTC（协调世界时）指示符||

  <!--Not Available on For more information on the ISO 8601 format, see [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)-->

## <a name="remarks"></a>备注

GetCurrentDateTime() 是非确定性的函数。 返回的结果为 UTC。 精度为 7 位，准确度为 100 纳秒。

此系统函数不会使用索引。

## <a name="examples"></a>示例

以下示例演示如何使用 GetCurrentDateTime() 内置函数获取当前 UTC 日期时间。

```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  

 下面是示例结果集。

```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>后续步骤

- [日期和时间函数 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)

<!-- Update_Description: update meta properties, wording update, update link -->