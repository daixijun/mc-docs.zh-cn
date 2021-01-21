---
title: Kusto 入门
description: 本文介绍 Kusto 入门。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/13/2020
ms.date: 01/22/2021
ms.localizationpriority: high
ms.openlocfilehash: dcabd97710b12b8108ffaca921ee9fc684be8c45
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611656"
---
# <a name="getting-started-with-kusto"></a>Kusto 入门

Azure 数据资源管理器是一项服务，用于存储和运行基于大数据的交互式分析。

它基于关系数据库管理系统，支持数据库、表和列等实体。 使用 Kusto 查询语言执行复杂的分析查询。 一些查询运算符包括：
* 计算列
* 搜索和筛选行
* 按聚合分组
* join 函数

此服务提供卓越的数据引入和查询性能： 
* 代价是无法就地更新单个行和跨表约束或事务。 
* 对于 OLTP 和数据仓库之类的方案而言，Kusto 是对传统 RDBMS 系统的补充，不会替换传统 RDBMS 系统。
* 结构化、半结构化（例如与 JSON 类似的嵌套类型）和非结构化（自由文本）数据的处理方式相同。

## <a name="interacting-with-azure-data-explorer"></a>与 Azure 数据资源管理器交互

用户与 Azure 数据资源管理器(Kusto) 进行交互的主要方式：
* 使用某一个[查询工具](../../tools-integrations-overview.md#azure-data-explorer-query-tools)。 
* [SQL 查询](../api/tds/t-sql.md)。
*  [Kusto 查询语言](../query/index.md)是交互的主要方式。 通过 KQL，可发送数据查询并使用[控制命令](../management/index.md)来管理实体和发现元数据等。
查询和控制命令都是短文本“程序”。

## <a name="kusto-queries"></a>Kusto 查询

查询是一种只读请求，用于处理数据并返回此处理的结果，而无需修改数据或元数据。 Kusto 查询可以使用 [SQL 语言](../api/tds/t-sql.md)或 [Kusto 查询语言](../query/index.md)。 以后者为例，以下查询计算 `Logs` 表中有多少行的 `Level` 列的值为字符串 `Critical`：

```kusto
Logs
| where Level == "Critical"
| count
```

> [!NOTE]
> 查询不能以句点 (`.`) 字符或哈希 (`#`) 字符开头。

## <a name="control-commands"></a>控制命令

控制命令是向 Kusto 发出的请求，请求处理数据或元数据并可能对其进行修改。 例如，以下控制命令创建一个新的 Kusto 表，该表包含两个列，即 `Level` 和 `Text`：

```kusto
.create table Logs (Level:string, Text:string)
```

控制命令有其自己的语法（该语法不属于 Kusto 查询语言语法，虽然二者的许多概念相同）。 具体说来，控制命令不同于查询的一点是：命令文本中的第一个字符为句点 (`.`)（此字符不能启动查询）。
这种区别可以防范许多种类的安全攻击，因为无法在查询中嵌入控制命令。

并非所有控制命令都修改数据或元数据。 以 `.show` 开头的一大类命令用于显示元数据或数据。 例如，`.show tables` 命令返回一个列表，其中包含当前数据库中的所有表。
