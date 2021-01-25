---
title: .show database - Azure 数据资源管理器 | Microsoft Docs
description: 本文介绍 Azure 数据资源管理器中的 .show database。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/13/2020
ms.date: 01/22/2021
ms.openlocfilehash: c790ae5692d5821dd9a1957d86f085fb37d9d14e
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611625"
---
# <a name="show-database"></a>.show database

返回一个表，其中显示了上下文数据库的属性。

若要返回一个表，其中每个记录都对应于用户可以访问的群集中的一个数据库，请参阅 [`.show databases`](show-databases.md)。

**语法**

`.show` `database` [`details` | `identity` | `policies` | `datastats`]

未指定任何选项的默认调用等于“identity”选项。

**“identity”选项的输出**
 
|输出参数 |类型 |说明 
|---|---|---
|DatabaseName  |String |数据库名称。 数据库名称区分大小写。 
|PersistentStorage  |String |在其中存储数据库的持久存储 URI。 （对于临时数据库，此字段为空。） 
|版本  |String |数据库的版本号。 此数字针对数据库中的每个更改操作（例如，添加数据和更改架构）进行更新。 
|IsCurrent  |布尔 |如果数据库是当前连接所指向的数据库，则为 True。 
|DatabaseAccessMode  |String |群集附加到数据库的方式。 例如，如果数据库是在只读模式下附加的，则群集以任何方式修改数据库的所有请求都将失败。 
|PrettyName |String |数据库友好名称
|CurrentUserIsUnrestrictedViewer |布尔 | 指定当前用户是否是数据库的不受限制的查看者。
|数据库 ID |Guid |数据库的唯一 ID。

**“details”选项的输出**
 
|输出参数 |类型 |说明 
|---|---|---
|DatabaseName  |String |数据库名称。 数据库名称区分大小写。 
|PersistentStorage  |String |在其中存储数据库的持久存储 URI。 （对于临时数据库，此字段为空。） 
|版本  |String |数据库版本号。 此数字针对数据库中的每个更改操作（例如，添加数据和更改架构）进行更新。 
|IsCurrent  |布尔 |如果数据库是当前连接所指向的数据库，则为 True。 
|DatabaseAccessMode  |String |群集附加到数据库的方式。 例如，如果数据库是在只读模式下附加的，则群集以任何方式修改数据库的所有请求都将失败。 
|PrettyName |String |数据库友好名称
|AuthorizedPrincipals |String | 数据库的授权主体集合（以 JSON 格式序列化）。
|RetentionPolicy |String | 数据库的保留策略（以 JSON 格式序列化）。
|MergePolicy |String | 数据库的“盘区合并”策略（以 JSON 格式序列化）。
|CachingPolicy |String | 数据库的“缓存”策略（以 JSON 格式序列化）。
|ShardingPolicy |String | 数据库的“分片”策略（以 JSON 格式序列化）。
|StreamingIngestionPolicy |String | 数据库的“流式引入”策略（以 JSON 格式序列化）。
|IngestionBatchingPolicy |String | 数据库的“引入批处理”策略（以 JSON 格式序列化）。
|TotalSize |Real | 数据库的盘区总大小。
|数据库 ID |Guid |数据库的唯一 ID。

**“policies”选项的输出**
 
|输出参数 |类型 |说明 
|---|---|---
|DatabaseName  |String |数据库名称。 数据库名称区分大小写。 
|PersistentStorage  |String |在其中存储数据库的持久存储 URI。 （对于临时数据库，此字段为空。） 
|版本  |String |数据库版本号。 此数字针对数据库中的每个更改操作（例如，添加数据和更改架构）进行更新。 
|IsCurrent  |布尔 |如果数据库是当前连接所指向的数据库，则为 True。 
|DatabaseAccessMode  |String |群集附加到数据库的方式。 例如，如果数据库是在只读模式下附加的，则群集以任何方式修改数据库的所有请求都将失败。 
|PrettyName |String |数据库的友好名称。
|数据库 ID |Guid |数据库的唯一 ID。
|AuthorizedPrincipals |String | 数据库的授权主体集合（以 JSON 格式序列化）。
|RetentionPolicy |String | 数据库的保留策略（以 JSON 格式序列化）。
|MergePolicy |String | 数据库的“盘区合并”策略（以 JSON 格式序列化）。
|CachingPolicy |String | 数据库的“缓存”策略（以 JSON 格式序列化）。
|ShardingPolicy |String | 数据库的“分片”策略（以 JSON 格式序列化）。
|StreamingIngestionPolicy |String | 数据库的“流式引入”策略（以 JSON 格式序列化）。
|IngestionBatchingPolicy |String | 数据库的“引入批处理”策略（以 JSON 格式序列化）。

**“datastats”选项的输出**

|输出参数 |类型 |说明 
|---|---|---
|DatabaseName  |String |数据库名称。 数据库名称区分大小写。
|PersistentStorage  |String |在其中存储数据库的持久存储 URI。 （对于临时数据库，此字段为空。） 
|版本  |String |数据库版本号。 此数字针对数据库中的每个更改操作（例如，添加数据和更改架构）进行更新。 
|IsCurrent  |布尔 |如果数据库是当前连接所指向的数据库，则为 True。 
|DatabaseAccessMode  |String |群集附加到数据库的方式。 例如，如果数据库是在只读模式下附加的，则群集以任何方式修改数据库的所有请求都将失败。 
|PrettyName |String |数据库的友好名称。
|数据库 ID |Guid |数据库的唯一 ID。
|OriginalSize |Real | 数据库的盘区总原始大小。
|ExtentSize |Real | 数据库的盘区总大小（数据 + 索引）。
|CompressedSize |Real | 数据库的盘区总数据压缩大小。
|IndexSize |Real | 数据库的盘区总索引大小。
|RowCount |Long | 数据库的盘区总行计数。
|HotOriginalSize |Real | 数据库的热盘区总原始大小。
|HotExtentSize |Real | 数据库的热盘区总大小（数据 + 索引）。
|HotCompressedSize |Real | 数据库的热盘区总数据压缩大小。
|HotIndexSize |Real | 数据库的热盘区总索引大小。
|HotRowCount |Long | 数据库的热盘区总行计数。