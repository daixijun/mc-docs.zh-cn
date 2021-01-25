---
title: 数据库游标 - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的数据库游标。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/13/2020
ms.date: 01/22/2021
ms.openlocfilehash: 4c296b80410adda514ab93d8b8fbea30d9ff145b
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611665"
---
# <a name="database-cursors"></a>数据库游标

数据库游标是一个数据库级对象，可用于多次查询数据库。 即使存在与查询并行发生的 `data-append` 或 `data-retention` 操作，也会获得一致的结果。

数据库游标旨在解决下述两个重要方案的问题：

* 只要查询指示“相同数据集”，就可以多次重复相同的查询并获得相同的结果。

* 能够进行“恰好一次”查询。 此查询仅“看到”先前查询未看到的数据（因为那时该数据不可用）。
   例如，可以使用该查询来循环访问表中所有新到达的数据，而不必担心处理同一记录两次或错误地跳过记录。

数据库游标在查询语言中表示为 `string` 类型的标量值。 应将实际值视为不透明值。除了保存其值或使用下面所述的游标函数外，不支持任何其他操作。

## <a name="cursor-functions"></a>游标函数

Kusto 提供三个函数来帮助实现上述两个方案：

* [cursor_current()](../query/cursorcurrent.md)：使用此函数可检索数据库游标的当前值。
   可以使用此值作为其他两个函数的参数。
   此函数还有一个同义词，即 `current_cursor()`。

* [cursor_after(rhs:string)](../query/cursorafterfunction.md)：此特殊函数可用于启用了 [IngestionTime 策略](ingestiontime-policy.md)的表记录。 它将返回 `bool` 类型的标量值，指示记录的 `ingestion_time()` 数据库游标值是否在 `rhs` 数据库游标值之后。

* [cursor_before_or_at(rhs:string)](../query/cursorbeforeoratfunction.md)：此特殊函数可用于启用了 [IngestionTime 策略](ingestiontime-policy.md)的表记录。 它返回一个 `bool` 类型的标量值，指示记录的 `ingestion_time()` 数据库游标值是在 `rhs` 数据库游标值之前还是在该值处。

这两个特殊函数（`cursor_after` 和 `cursor_before_or_at`）也有副作用：使用这两个函数时，Kusto 会将 **数据库游标的当前值** 发送到查询的 `@ExtendedProperties` 结果集。 游标的属性名称为 `Cursor`，其值为一个 `string`。 

例如：

```json
{"Cursor" : "636040929866477946"}
```

## <a name="restrictions"></a>限制

数据库游标只能用于已启用 [IngestionTime 策略](ingestiontime-policy.md)的表。 此类表中的每条记录都与引入记录时有效的数据库游标的值相关联。
因此，可以使用 [ingestion_time()](../query/ingestiontimefunction.md) 函数。

除非数据库包含至少一个定义了 [IngestionTime 策略](ingestiontime-policy.md)的表，否则数据库游标对象不保存任何有意义的值。
系统会确保根据引入历史记录的需要将该值更新到此类表中，然后运行引用此类表的查询。 在其他情况下，它不一定会更新。

引入过程首先会提交数据，使其可用于查询，然后才会为每条记录分配一个实际的游标值。 如果尝试在引入完成后立即使用数据库游标查询数据，则结果可能尚未包含最后添加的记录，因为尚未为这些记录分配游标值。 同样，即使引入在两次检索之间进行，重复检索当前数据库游标值也可能返回相同的值，因为只有游标提交才能更新其值。

## <a name="example-processing-records-exactly-once"></a>示例：仅处理一次记录

对于架构为 `[Name, Salary]` 的表 `Employees`，若要在新记录引入到表中时连续处理这些记录，请使用以下过程：

```kusto
// [Once] Enable the IngestionTime policy on table Employees
.set table Employees policy ingestiontime true

// [Once] Get all the data that the Employees table currently holds 
Employees | where cursor_after('')

// The query above will return the database cursor value in
// the @ExtendedProperties result set. Lets assume that it returns
// the value '636040929866477946'

// [Many] Get all the data that was added to the Employees table
// since the previous query was run using the previously-returned
// database cursor 
Employees | where cursor_after('636040929866477946') // -> 636040929866477950

Employees | where cursor_after('636040929866477950') // -> 636040929866479999

Employees | where cursor_after('636040929866479999') // -> 636040939866479000
```
