---
title: 管理（控制命令）概述 - Azure 数据资源管理器 | Microsoft Docs
description: 本文概述了 Azure 数据资源管理器中的管理（控制命令）。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/13/2020
ms.date: 01/22/2021
ms.openlocfilehash: ebd5dbca7ff6f5397a5f7f2ce596a40c78746529
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611684"
---
# <a name="management-control-commands-overview"></a>管理（控制命令）概述

本部分介绍用于管理 Kusto 的控制命令。
控制命令是发送给服务的请求，用于检索数据库表中不一定是数据的信息，或者用于修改服务状态，等等。

## <a name="differentiating-control-commands-from-queries"></a>区分控制命令和查询

Kusto 使用三种机制来区分查询和控制命令：语言级别机制、协议级别机制，以及 API 级别机制。 这样做是为了安全。

在语言级别，可以通过请求文本的第一个字符来确定该请求是控制命令还是查询。 控制命令必须以点 (`.`) 字符开头，而查询则不能以该字符开头。

在协议级别，可以将不同的 HTTP/HTTPS 终结点用于控制命令而不是查询。

在 API 级别，可以将不同的函数用于发送控制命令而不是查询。

## <a name="combining-queries-and-control-commands"></a>组合使用查询和控制命令

控制命令可以引用查询（反之则不行）或其他控制命令。
有几个支持的方案：

1. **AdminThenQuery**：执行某个控制命令，将其结果（以临时数据表的形式呈现）充当某个查询的输入。
2. **AdminFromQuery**：执行某个查询或 `.show` 管理命令，将其结果（以临时数据表的形式呈现）充当某个控制命令的输入。

请注意，不管什么情况，整个组合从技术上来说是一个控制命令，而不是一个查询，因此请求的文本必须以点 (`.`) 字符开头，而且请求必须发送到服务的管理终结点。

另请注意，[查询语句](../query/statements.md)出现在文本的查询部分（不能将它们置于命令本身之前）。

>[!NOTE]
> 不要太频繁地运行 [command-then-query] 操作。
> command-then-query 将通过管道传输控制命令的结果集并对其应用筛选器/聚合。
>  * 例如： `.show ... | where ... | summarize ...`
>   * 运行 `.show cluster extents | count`（强调 `| count`）之类的内容时，Kusto 会先准备一个数据表，它包含群集中所有盘区的所有详细信息。 然后，系统将这个仅限内存中的表发送到引擎来进行计数。 实际上，为了向你提供这样简单的答案，系统也要在非优化的路径中费力操作。


**AdminThenQuery** 以下述两种方式之一进行指示：

1. 如果使用竖线 (`|`) 字符，则查询可以将控制命令的结果视为与任何其他的数据生成查询运算符的结果一样。
2. 如果使用分号 (`;`) 字符，则会将控制命令的结果引入名为 `$command_results` 的特殊符号中，该符号随即可以用在查询中，不限次数。

例如：

```kusto
// 1. Using pipe: Count how many tables are in the database-in-scope:
.show tables
| count

// 2. Using semicolon: Count how many tables are in the database-in-scope:
.show tables;
$command_results
| count

// 3. Using semicolon, and including a let statement:
.show tables;
let useless=(n:string){strcat(n,'-','useless')};
$command_results | extend LastColumn=useless(TableName)
```

**AdminFromQuery** 通过 `<|` 字符组合进行指示。 例如，在下面的代码中，我们先执行一个查询，生成一个包含单个列（名称为 `str`，类型为 `string`）和单个行的表，并将其以表名 `MyTable` 写入数据库的上下文中：

```kusto
.set MyTable <|
let text="Hello, World!";
print str=text
```


