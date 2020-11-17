---
title: datatable 运算符 - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 datatable 运算符。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
origin.date: 02/13/2020
ms.date: 10/29/2020
zone_pivot_group_filename: zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 8b744d8bd9cbc0297d9b2e447da74e3d39c05c59
ms.sourcegitcommit: 39288459139a40195d1b4161dfb0bb96f5b71e8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94590907"
---
# <a name="datatable-operator"></a>datatable 运算符

返回一个表，在查询本身中定义其架构和值。

> [!NOTE]
> 此运算符没有管道输入。

## <a name="syntax"></a>语法

`datatable` `(` *ColumnName* `:` *ColumnType* [`,` ...] `)` `[` *ScalarValue* [`,` *ScalarValue* ...] `]`

## <a name="arguments"></a>自变量

::: zone pivot="azuredataexplorer"

* ColumnName、ColumnType：这些参数定义表的架构。 参数使用的语法与定义表时使用的语法相同。
  有关详细信息，请参阅 [.create table](../management/create-table-command.md)。
* ScalarValue：要插入到表中的常数标量值。 值数必须是表中列的整数倍。 第 n 个值的类型必须与列 n % NumColumns 相对应。

::: zone-end

::: zone pivot="azuremonitor"

* ColumnName、ColumnType：这些参数定义表的架构。
* ScalarValue：要插入到表中的常数标量值。 值数必须是表中列的整数倍。 第 n 个值的类型必须与列 n % NumColumns 相对应。

::: zone-end

## <a name="returns"></a>返回

此运算符返回给定架构和数据的数据表。

## <a name="example"></a>示例

```kusto
datatable (Date:datetime, Event:string)
    [datetime(1910-06-11), "Born",
     datetime(1930-01-01), "Enters Ecole Navale",
     datetime(1953-01-01), "Published first book",
     datetime(1997-06-25), "Died"]
| where strlen(Event) > 4
```
