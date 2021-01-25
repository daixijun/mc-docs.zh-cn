---
title: make_bag()（聚合函数）- Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 make_bag() 聚合函数。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
origin.date: 02/13/2020
ms.date: 01/22/2021
ms.openlocfilehash: 060c3369b24bc3dce5299eafa3ab741f101bd905
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611619"
---
# <a name="make_bag-aggregation-function"></a>make_bag()（聚合函数）

返回组中 `Expr` 的所有值的 `dynamic` (JSON) 属性包（字典）。

* 只能在 [summarize](summarizeoperator.md) 内的聚合上下文中使用

## <a name="syntax"></a>语法

`summarize` `make_bag(`*`Expr`* [`,` *MaxSize*]`)`

## <a name="arguments"></a>参数

* Expr：用于聚合计算的 `dynamic` 类型的表达式。
* MaxSize 是对返回元素最大数目的可选整数限制。 默认值为 1048576。 MaxSize 值不能超过 1048576。

> [!NOTE]
> `make_dictionary()` 是 `make_bag()` 的旧的过时版本。 旧版本的默认限制为 MaxSize = 128。

## <a name="returns"></a>返回

返回组（属性包）中 `Expr` 的所有值的 `dynamic` (JSON) 属性包（字典）。
将跳过非字典值。
如果一个键出现在多个行中，则会从此键的可能值中选择一个任意值。

## <a name="see-also"></a>另请参阅

使用 [bag_unpack()](bag-unpackplugin.md) 插件将动态 JSON 对象扩展到使用属性包键的列中。 

## <a name="examples"></a>示例

```kusto
let T = datatable(prop:string, value:string)
[
    "prop01", "val_a",
    "prop02", "val_b",
    "prop03", "val_c",
];
T
| extend p = pack(prop, value)
| summarize dict=make_bag(p)

```

|dict|
|----|
|{ "prop01": "val_a", "prop02": "val_b", "prop03": "val_c" } |

使用 [bag_unpack()](bag-unpackplugin.md) 插件将 make_bag() 输出中的包键转换为列。 

```kusto
let T = datatable(prop:string, value:string)
[
    "prop01", "val_a",
    "prop02", "val_b",
    "prop03", "val_c",
];
T
| extend p = pack(prop, value)
| summarize bag=make_bag(p)
| evaluate bag_unpack(bag) 

```

|prop01|prop02|prop03|
|---|---|---|
|val_a|val_b|val_c|
