---
title: Let 语句 - Azure 数据资源管理器
description: 本文介绍了 Azure 数据资源管理器中的 Let 语句。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
origin.date: 08/09/2020
ms.date: 01/22/2021
ms.localizationpriority: high
ms.openlocfilehash: 3ce4147ac86faed1948db3754ae051f95542f46c
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611621"
---
# <a name="let-statement"></a>Let 语句

Let 语句将名称绑定到表达式。 对于作用域中出现了 let 语句的其余部分，可以使用名称来引用其绑定值。 Let 语句的作用域可以是全局，也可以是某个函数主体。
如果该名称以前绑定到另一个值，则使用“innermost”let 语句绑定。

Let 语句改善了模块化和重用，因为它们允许你将可能复杂的表达式分解为多个部分。
每个部分都通过 let 语句绑定到一个名称，它们共同构成了整个表达式。 它们还可用于创建用户定义的函数和视图。 视图是基于表的表达式，其结果看起来像新表。

> [!NOTE]
> Let 语句绑定的名称必须是有效的实体名称。

Let 语句绑定的表达式可以是：
* 标量类型
* 表格类型
* 用户定义的函数 (lambda)

## <a name="syntax"></a>语法

`let` *Name* `=` *ScalarExpression* | *TabularExpression* | *FunctionDefinitionExpression*

|字段  |定义  |示例  |
|---------|---------|---------|
|*名称*   | 要绑定的名称。 名称必须是有效的实体名称。    |允许实体名称转义，例如 `["Name with spaces"]`。      |
|*ScalarExpression*     |  具有标量结果的表达式，其值绑定到名称。  | `let one=1;`  |
|*TabularExpression*    | 具有表格结果的表达式，其值绑定到名称。   | `Logs | where Timestamp > ago(1h)`    |
|*FunctionDefinitionExpression*   | 一个表达式，该表达式生成将绑定到名称的 lambda（匿名函数声明）。   |  `let f=(a:int, b:string) { strcat(b, ":", a) }`  |


### <a name="lambda-expressions-syntax"></a>Lambda 表达式语法

[`view`] `(`[*TabularArguments*][`,`][*ScalarArguments*]`)` `{` *FunctionBody* `}`

`TabularArguments` - [*TabularArgName* `:` `(`[*AtrName* `:` *AtrType*] [`,` ... ]`)`] [`,` ... ][`,`]

 或者：

 [TabularArgName `:` `(` `*` `)`] - 指示包含变量列和未知列的表格表达式。

`ScalarArguments` - [*ArgName* `:` *ArgType*] [`,` ... ]


|字段  |定义  |示例  |
|---------|---------|---------|
| **view** | 只能出现在没有实参的无参数 let 语句中。 使用“view”关键字时，let 语句将包含在使用 `union` 运算符的查询中，并通过通配符选择表/视图。 |  |
| **_TabularArguments_* _ | 形式表格表达式自变量的列表。 
| 每个表格自变量都有：||
|<ul><li> TabularArgName</li></ul> | 形式表格自变量的名称。 该名称可以出现在 FunctionBody 中，并在调用 lambda 时绑定到特定值。 ||
|<ul><li>表架构定义 </li></ul> | 属性及其类型的列表| AtrName：AtrType|
| ScalarArguments*_ | 形式标量自变量的列表。 
|每个标量自变量都有：||
|<ul><li>ArgName</li></ul> | 形式标量自变量的名称。 该名称可以出现在 FunctionBody 中，并在调用 lambda 时绑定到特定值。  |
| <ul><li>*ArgType* </li></ul>| 形式标量自变量的类型。 | 目前仅支持使用以下类型作为 lambda 参数类型：`bool`、`string`、`long`、`datetime`、`timespan`、`real` 和 `dynamic`（以及这些类型的别名）。

> [!NOTE]
>在 lambda 调用中使用的表格表达式必须包含（但不限于）类型匹配的所有属性。
>
>`(*)` 可用作表格表达式。 
>
> 任何表格表达式都可以用在 lambda 调用中，但其列都不能在 lambda 表达式中访问。 
>
> 所有表格参数都应位于标量参数之前。

## <a name="multiple-and-nested-let-statements"></a>多个嵌套的 let 语句

多个 let 语句可以一起使用，以分号 `;` 作为分隔符，如以下示例所示。

> [!NOTE]
> 最后一个语句必须是有效的查询表达式。 

```kusto
let start = ago(5h); 
let period = 2h; 
T | where Time > start and Time < start + period | ...
```

允许使用嵌套的 let 语句，可将其用在 lambda 表达式中。
Let 语句和自变量在函数主体的当前作用域和内部作用域中可见。

```kusto
let start_time = ago(5h); 
let end_time = start_time + 2h; 
T | where Time > start_time and Time < end_time | ...
```

## <a name="examples"></a>示例

### <a name="use-let-function-to-define-constants"></a>使用 let 函数定义常量

下面的示例将名称 `x` 绑定到标量文本 `1`，然后在表格表达式语句中使用它。

```kusto
let x = 1;
range y from x to x step x
```

此示例与前一个示例类似，在使用 `['name']` 表示法时仅提供 let 语句的名称。

```kusto
let ['x'] = 1;
range y from x to x step x
```

### <a name="use-let-for-scalar-values"></a>将 let 用于标量值

```kusto
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

### <a name="use-let-statement-with-arguments-for-scalar-calculation"></a>使用带有自变量的 let 语句进行标量计算

此示例使用带有自变量的 let 语句进行标量计算。 该查询定义了用于将两个数字相乘的函数 `MultiplyByN`。

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
let MultiplyByN = (val:long, n:long) { val * n };
range x from 1 to 5 step 1 
| extend result = MultiplyByN(x, 5)
```

|x|result|
|---|---|
|1|5|
|2|10|
|3|15|
|4|20|
|5|25|

以下示例删除输入中的前导/尾随 `1`。

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
let TrimOnes = (s:string) { trim("1", s) };
range x from 10 to 15 step 1 
| extend result = TrimOnes(tostring(x))
```

|x|result|
|---|---|
|10|0|
|11||
|12|2|
|13|3|
|14|4|
|15|5|


### <a name="use-multiple-let-statements"></a>使用多个 let 语句

此示例定义了两个 let 语句，其中一个语句 (`foo2`) 使用另一个语句 (`foo1`)。

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
let foo1 = (_start:long, _end:long, _step:long) { range x from _start to _end step _step};
let foo2 = (_step:long) { foo1(1, 100, _step)};
foo2(2) | count
// Result: 50
```

### <a name="use-the-view-keyword-in-a-let-statement"></a>在 let 语句中使用 `view` 关键字

此示例演示了如何将 let 语句与 `view` 关键字一起使用。

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
let Range10 = view () { range MyColumn from 1 to 10 step 1 };
let Range20 = view () { range MyColumn from 1 to 20 step 1 };
search MyColumn == 5
```

|$table|MyColumn|
|---|---|
|Range10|5|
|Range20|5|


### <a name="use-materialize-function"></a>使用 materialize 函数

[`materialize`](materializefunction.md) 函数允许在执行查询时缓存子查询结果。 

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
let totalPagesPerDay = PageViews
| summarize by Page, Day = startofday(Timestamp)
| summarize count() by Day;
let materializedScope = PageViews
| summarize by Page, Day = startofday(Timestamp);
let cachedResult = materialize(materializedScope);
cachedResult
| project Page, Day1 = Day
| join kind = inner
(
    cachedResult
    | project Page, Day2 = Day
)
on Page
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1
```

|第 1 天|第 2 天|百分比|
|---|---|---|
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|34.0645725975255|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.618368960101|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.6291376489636|
