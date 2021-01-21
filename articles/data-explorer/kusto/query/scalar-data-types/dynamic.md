---
title: dynamic 数据类型 - Azure 数据资源管理器 | Microsoft Docs
description: 本文介绍了 Azure 数据资源管理器中的 dynamic 数据类型。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
origin.date: 07/09/2020
ms.date: 01/22/2021
ms.localizationpriority: high
ms.openlocfilehash: 4f6b8a33fe7314618cd0cf239703a5eb83ffa12f
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611427"
---
# <a name="the-dynamic-data-type"></a>dynamic 数据类型

`dynamic` 标量数据类型很特殊，因为它可以接受以下列表中的其他标量数据类型以及数组和属性包的任何值。 具体而言，`dynamic` 值可以是：

* NULL。
* 下述任何基元标量数据类型的值：`bool`、`datetime`、`guid`、`int`、`long`、`real`、`string` 和 `timespan`。
* `dynamic` 值的数组，其中包含零个或零个以上的值，并从零开始编制索引。
* 一个属性包，可将唯一的 `string` 值映射到 `dynamic` 值。
  此属性包有零个或零个以上的此类映射（称为“槽”），通过唯一的 `string` 值进行索引编制。 这些槽是无序的。

> [!NOTE]
> * `dynamic` 类型的值的限制为 1MB (2^20)。
> * 虽然 `dynamic` 类型看起来类似于 JSON，但是它可以保存 JSON 模型无法表示的不存在于 JSON 中的值（例如，`long`、`real`、`datetime`、`timespan` 和 `guid`）。
>   因此，在将 `dynamic` 值序列化为 JSON 表示形式时，JSON 无法表示的值将被序列化为 `string` 值。 与之相反，Kusto 会将字符串作为强类型值进行分析（如果它们可以这样进行分析）。
>   这适用于 `datetime`、`real`、`long` 和 `guid` 类型。 
>   有关 JSON 对象模型的详细信息，请参阅 [json.org](https://json.org/)。
> * Kusto 不会尝试保留属性包中从名称到值的映射的顺序，因此你不能假定顺序会保留。 例如，将两个具有相同映射集的属性包表示为 `string` 值时，它们完全有可能产生不同的结果。

## <a name="dynamic-literals"></a>动态文本

类型为 `dynamic` 的文本如下所示：

`dynamic(` *Value* `)`

*Value* 可以是：

* `null`，在这种情况下，文本表示 null 动态值：`dynamic(null)`。
* 另一种标量数据类型文本，在这种情况下，文本表示“内部”类型的 `dynamic` 文本。 例如，`dynamic(4)` 是一个动态值，其中的值 4 为长标量数据类型。
* 动态文本或其他类型文本的数组：`[` *ListOfValues* `]`。 例如，`dynamic([1, 2, "hello"])` 是由三个元素组成的动态数组，两个为 `long` 值，一个为 `string` 值。
* 属性包：`{` *Name* `=` *Value* ... `}`。 例如，`dynamic({"a":1, "b":{"a":2}})` 是具有两个槽（`a` 和 `b`）的属性包，第二个槽是另一个属性包。

```kusto
print o=dynamic({"a":123, "b":"hello", "c":[1,2,3], "d":{}})
| extend a=o.a, b=o.b, c=o.c, d=o.d
```

为方便起见，还可以让查询文本自身中出现的 `dynamic` 文本包含以下类型的其他 Kusto 文本：`datetime`、`timespan`、`real`、`long`、`guid`、`bool` 和 `dynamic`。
在分析字符串时（例如在使用 `parse_json` 函数或引入数据时），此基于 JSON 的扩展不可用，但可以通过它执行以下操作：

```kusto
print d=dynamic({"a": datetime(1970-05-11)})
```

若要将遵循 JSON 编码规则的 `string` 值分析为 `dynamic` 值，请使用 `parse_json` 函数。 例如：

* `parse_json('[43, 21, 65]')` - 数字数组
* `parse_json('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` - 字典
* `parse_json('21')` - 包含数字的动态类型的单个值
* `parse_json('"21"')` - 包含字符串的动态类型的单个值
* `parse_json('{"a":123, "b":"hello", "c":[1,2,3], "d":{}}')` - 提供与上例中的 `o` 相同的值。

> [!NOTE]
> 与 JavaScript 不同，JSON 要求使用双引号 (`"`) 字符括住字符串和属性包属性名称。
> 因此，使用单引号 (`'`) 字符括住 JSON 编码的字符串文本通常更简单。
  
以下示例展示了如何定义一个保存 `dynamic` 列（以及 `datetime` 列）的表，然后将单个记录引入其中。 它还演示了如何对 CSV 文件中的 JSON 字符串进行编码：

```kusto
// dynamic is just like any other type:
.create table Logs (Timestamp:datetime, Trace:dynamic)

// Everything between the "[" and "]" is parsed as a CSV line would be:
// 1. Since the JSON string includes double-quotes and commas (two characters
//    that have a special meaning in CSV), we must CSV-quote the entire second field.
// 2. CSV-quoting means adding double-quotes (") at the immediate beginning and end
//    of the field (no spaces allowed before the first double-quote or after the second
//    double-quote!)
// 3. CSV-quoting also means doubling-up every instance of a double-quotes within
//    the contents.
.ingest inline into table Logs
  [2015-01-01,"{""EventType"":""Demo"", ""EventValue"":""Double-quote love!""}"]
```

|Timestamp                   | 跟踪                                                 |
|----------------------------|-------------------------------------------------------|
|2015-01-01 00:00:00.0000000 | {"EventType":"Demo","EventValue":"Double-quote love!"}|

## <a name="dynamic-object-accessors"></a>动态对象访问器

若要对字典执行下标操作，请使用点表示法 (`dict.key`) 或方括号表示法 (`dict["key"]`)。
如果下标为字符串常量，则这两个选项是等效的。

> [!NOTE] 
> 若要使用表达式作为下标，请使用方括号表示法。 使用算术表达式时，方括号内的表达式必须括在圆括号中。

在下面的示例中，`dict` 和 `arr` 是 dynamic 类型的列：

|表达式                        | 访问器表达式类型 | 含义                                                                              | 注释                                      |
|----------------------------------|--------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------|
|dict[col]                         | 实体名称（列）     | 使用 `col` 列的值作为键对字典执行下标操作              | 列必须为字符串类型                 | 
|arr[index]                        | 实体索引（列）    | 使用 `index` 列的值作为索引对数组执行下标操作              | 列必须为整数或布尔类型     | 
|arr[-index]                       | 实体索引（列）    | 从数组末尾检索 'index'-th 值                             | 列必须为整数或布尔类型     |
|arr[(-1)]                         | 实体索引             | 检索数组中的最后一个值                                                |                                               |
|arr[toint(indexAsString)]         | 函数调用            | 将 `indexAsString` 列的值强制转换为 int，并使用它们对数组执行下标操作 |                                               |
|dict[['where']]                   | 用作实体名称（列）的关键字 | 使用 `where` 列的值作为键对字典执行下标操作    | 与某些查询语言关键字相同的实体名称必须用引号引起来 | 
|dict.['where'] 或 dict['where']   | 常数                 | 使用 `where` 字符串作为键对字典执行下标操作                              |                                               |

**性能提示：** 尽可能使用常数下标

即使子对象具有不同的基础类型，访问 `dynamic` 值的子对象也会产生另一个 `dynamic` 值。 可以使用 `gettype` 函数查明值的实际基础类型，并使用下面列出的任何类型强制转换函数将其强制转换为实际类型。

## <a name="casting-dynamic-objects"></a>强制转换动态对象

> 对动态对象执行下标操作之后，必须将值强制转换为简单类型。

|表达式 | 值 | 类型|
|---|---|---|
| X | parse_json('[100,101,102]')| array|
|X[0]|parse_json('100')|动态|
|toint(X[1])|101| int|
| Y | parse_json('{"a1":100, "a b c":"2015-01-01"}')| dictionary|
|Y.a1|parse_json('100')|动态|
|Y["a b c"]| parse_json("2015-01-01")|动态|
|todate(Y["a b c"])|datetime(2015-01-01)| datetime|

强制转换函数包括：

* `tolong()`
* `todouble()`
* `todatetime()`
* `totimespan()`
* `tostring()`
* `toguid()`
* `todynamic()`

## <a name="building-dynamic-objects"></a>生成动态对象

可以使用多个函数创建新的 `dynamic` 对象：

* [pack()](../packfunction.md) 通过名称/值对创建属性包。
* [pack_array()](../packarrayfunction.md) 通过名称/值对创建数组。
* [range()](../rangefunction.md) 创建一个包含数字算术序列的数组。
* [zip()](../zipfunction.md) 将两个数组中的“并行”值配对成一个数组。
* [repeat()](../repeatfunction.md) 创建一个包含重复值的数组。

此外，还有多个聚合函数可用于创建 `dynamic` 数组来保存聚合值：

* [buildschema()](../buildschema-aggfunction.md) 返回包含多个 `dynamic` 值的聚合架构。
* [make_bag()](../make-bag-aggfunction.md) 返回一个在组内包含动态值的属性包。
* [make_bag_if()](../make-bag-if-aggfunction.md) 返回一个在组内包含动态值的属性包（带谓词）。
* [make_list()](../makelist-aggfunction.md) 返回一个按顺序保存所有值的数组。
* [make_list_if()](../makelistif-aggfunction.md) 返回一个按顺序保存所有值的数组（带谓词）。
* [make_list_with_nulls()](../make-list-with-nulls-aggfunction.md) 返回一个按顺序保存所有值（包括 NULL 值）的数组。
* [make_set()](../makeset-aggfunction.md) 返回一个保存所有唯一值的数组。
* [make_set_if()](../makesetif-aggfunction.md) 返回一个保存所有唯一值的数组（带谓词）。

## <a name="operators-and-functions-over-dynamic-types"></a>基于 dynamic 类型的运算符和函数

有关标量动态/数组函数的完整列表，请参阅[动态/数组函数](../scalarfunctions.md#dynamicarray-functions)。

|运算符或函数|dynamic 数据类型的用法|
|---|---|
| *value* `in` *array*| 如果有 = = *value* 的 *array* 元素，则为 true<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| 如果没有 == *value* 的 *array* 元素，则为 true
|[array](../arraylengthfunction.md)`array_length(``)`| 如果不是数组，则为 null
|[`bag_keys(`bag`)`](../bagkeysfunction.md)| 枚举 dynamic 属性包对象中的所有根键。
|[`bag_merge(`bag1,...,bagN`)`](../bag-merge-function.md)| 将多个 dynamic 属性包合并成一个包含所有属性的 dynamic 属性包。
|[`extractjson(`path,object`)`](../extractjsonfunction.md)|使用路径导航到对象。
|[`parse_json(`source`)`](../parsejsonfunction.md)| 将 JSON 字符串转换为动态对象。
|[`range(`from,to,step`)`](../rangefunction.md)| 值组成的数组
|[`mv-expand` listColumn](../mvexpandoperator.md) | 为指定单元格列表中的每个值复制一行。
|[`summarize buildschema(`column`)`](../buildschema-aggfunction.md) |根据列内容推断类型架构
|[`summarize make_bag(`column`)`](../make-bag-aggfunction.md) | 将列中的属性包（字典）值合并成一个属性包，无需进行键复制。
|[`summarize make_bag_if(`column,predicate`)`](../make-bag-if-aggfunction.md) | 将列中的属性包（字典）值合并成一个属性包，无需进行键复制（带谓词）。
|[`summarize make_list(`column`)` ](../makelist-aggfunction.md)| 平展行组并将列的值放入数组。
|[`summarize make_list_if(`column,predicate`)` ](../makelistif-aggfunction.md)| 平展行组并将列的值放入数组中（带谓词）。
|[`summarize make_list_with_nulls(`column`)` ](../make-list-with-nulls-aggfunction.md)| 平展行组并将列的值（包括 NULL 值）放入数组中。
|[`summarize make_set(`column`)`](../makeset-aggfunction.md) | 平展行组并将列的值放入数组，不进行复制。
