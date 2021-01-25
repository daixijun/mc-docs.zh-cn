---
title: .show cluster databases - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 .show cluster databases。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/13/2020
ms.date: 01/22/2021
ms.openlocfilehash: cf30b4e124f40ea2483126a97296a195a866b8ea
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611628"
---
# <a name="show-cluster-databases"></a>.show cluster databases

返回一个表，其中显示附加到该群集以及附加到调用该命令的用户有权访问的群集的所有数据库。 如果使用特定数据库名称，则仅包含这些数据库。

**语法**

`.show` `cluster` `databases` [`details` | `identity` | `policies` | `datastats`]

`.show` `cluster` `databases` `(`database1`,` database2`,` ... databaseN`)`

**输出**
 
|输出参数 |类型 |说明 
|---|---|---
|DatabaseName  |String |数据库名称。 数据库名称区分大小写。 
|PersistentStorage  |String |在其中存储数据库的持久存储 URI。 （对于临时数据库，此字段为空。） 
|版本  |String |数据库的版本号。 对于数据库中的每个更改操作（如添加数据和更改架构），都会更新此数字。 
|IsCurrent  |布尔 |如果数据库是当前连接所指向的数据库，则为 True。 
|DatabaseAccessMode  |String |群集附加到数据库的方式。 例如，如果数据库是在只读模式下附加的，则群集以任何方式修改数据库的所有请求都将失败。 
|PrettyName |String |数据库友好名称。
|CurrentUserIsUnrestrictedViewer |布尔 | 指定当前用户是否是数据库的不受限制的观看者。
|数据库 ID |Guid |数据库的唯一 ID。