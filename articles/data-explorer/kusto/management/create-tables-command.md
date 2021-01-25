---
title: .create tables - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 .create tables。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
origin.date: 07/05/2020
ms.date: 01/22/2021
ms.openlocfilehash: c16191668c77e840868f97a68f71da102a7df3c7
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611649"
---
# <a name="create-tables"></a>.create tables

创建新的空表作为批量操作。

该命令必须在特定数据库的上下文中运行。

需要[数据库用户权限](../management/access-control/role-based-authorization.md)。

**语法**

`.create` `tables` *TableName1* ([columnName:columnType], ...) [`,` *TableName2* ([columnName:columnType], ...) ... ] [`with` `(` `folder` `=` *FolderName*] `)`]

如果已经存在任何表，命令将返回成功。
 
**示例** 

```kusto
.create tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```
