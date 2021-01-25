---
title: .create table - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 .create table。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/06/2020
ms.date: 01/22/2021
ms.openlocfilehash: 52b08a168237544393bbd75fe2d76920b91c4df4
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611650"
---
# <a name="create-table"></a>.create table

创建新的空表。

该命令必须在特定数据库的上下文中运行。

需要[数据库用户权限](../management/access-control/role-based-authorization.md)。

**语法**

`.create` `table` *TableName* ([columnName:columnType], ...)  [`with` `(`[`docstring` `=` *Documentation*] [`,` `folder` `=` *FolderName*] `)`]

如果该表已存在，则此命令会返回成功。

**示例** 

```kusto
.create table MyLogs ( Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32 ) 
```
 
**返回输出**

以 JSON 格式返回表的架构，与下面的命令相同：

```kusto
.show table MyLogs schema as json
```

> [!NOTE]
> 若要创建多个表，请使用 [`.create tables`](create-tables-command.md) 命令以提高性能并降低群集负载。
