---
title: Null 值 - Azure 数据资源管理器 | Microsoft Docs
description: 本文介绍 Azure 数据资源管理器中的 Null 值。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/13/2020
ms.date: 08/18/2020
zone_pivot_group_filename: zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 31aafffe5fbc233a43473da58c9c0ff837ce79d2
ms.sourcegitcommit: 39288459139a40195d1b4161dfb0bb96f5b71e8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94590903"
---
# <a name="null-values"></a>Null 值

Kusto 中的所有标量数据类型都有表示缺失值的特殊值。
此值称为“null 值”，简称“null” 。

## <a name="null-literals"></a>null 文本

标量类型 `T` 的 null 值由 null 文本 `T(null)` 以查询语言表示。
因此，以下语句返回一个单行，其中包含的值全是 null 值：

```kusto
print bool(null), datetime(null), dynamic(null), guid(null), int(null), long(null), real(null), double(null), time(null)
```

> [!WARNING]
> 请注意，当前 `string` 类型不支持 null 值。

## <a name="comparing-null-to-something"></a>将 null 与某个值进行比较

null 值不等于该数据类型的任何其他值，包括它本身。 （也就是说，`null == null` 为 false。）若要确定某个值是否为 null 值，请使用 [isnull()](../isnullfunction.md) 函数和 [isnotnull()](../isnotnullfunction.md) 函数。

## <a name="binary-operations-on-null"></a>针对 null 的二进制运算

通常，null 以“粘性”方式围绕二进制运算符运行；null 值和任何其他值（包括另一个 null 值）之间的二进制运算生成 null 值。

## <a name="data-ingestion-and-null-values"></a>数据引入和 null 值

对于大多数数据类型，数据源中的缺失值在相应的表单元格中生成 null 值。 对此，例外情况是类型为 `string` 的列和类似 CSV 的引入，此情况下缺失的值生成空字符串。
例如，如果有以下情况： 

```kusto
.create table T [a:string, b:int]

.ingest inline into table T
[,]
[ , ]
[a,1]
```

那么：

|a     |b     |isnull(a)|isempty(a)|strlen(a)|isnull(b)|
|------|------|---------|----------|---------|---------|
|&nbsp;|&nbsp;|false    |true      |0        |true     |
|&nbsp;|&nbsp;|false    |false     |1        |是     |
|a     |1     |false    |false     |1        |false    |

::: zone pivot="azuredataexplorer"

* 如果在 Kusto.Explorer 中运行上述查询，则所有 `true` 值将显示为 `1`，所有 `false` 值将显示为 `0`。

::: zone-end

* Kusto 未提供用于限制表列，使其不具有 null 值的方法（换句话说，SQL 的 `NOT NULL` 约束没有对等项）。
