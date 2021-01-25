---
title: .alter table docstring - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 `.alter table docstring` 命令。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/04/2020
ms.date: 01/22/2021
ms.openlocfilehash: bcb46ba53cee4a8122d138220eb0c72cf929c8db
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611342"
---
# <a name="alter-table-docstring"></a>.alter table docstring

更改现有表的 DocString 值。

`.alter` `table` TableName `docstring` Documentation 

> [!NOTE]
> * 需要[数据库管理员权限](../management/access-control/role-based-authorization.md)
> * 允许最初创建表的[数据库用户](../management/access-control/role-based-authorization.md)对其进行修改
> * 如果该表不存在，则返回错误。 若要创建新表，请参阅 [`.create table`](create-table-command.md)

**示例** 

```kusto
.alter table LyricsAsTable docstring "This is the theme to Garry's show"
```
 
