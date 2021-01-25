---
title: 存储的查询结果（预览）- Azure 数据资源管理器
description: 本文介绍了如何在 Azure 数据资源管理器中创建和使用存储的查询结果。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: mispecto
ms.service: data-explorer
ms.topic: reference
origin.date: 12/03/2020
ms.date: 01/22/2021
ms.openlocfilehash: b334a9ca8f9f9154a2d08fa1edbb7b21833c9a58
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614887"
---
# <a name="stored-query-results-preview"></a>存储的查询结果（预览）

存储的查询结果是一种将查询结果暂时存储在服务中的机制。 可以在以后的查询中引用此数据。
请使用一个命令创建存储的查询对象，该命令使用已创建实体和已执行查询的名称。
该命令会返回查询生成的记录的子集（称为“预览”），但会存储所有记录。

在以下情况下，存储的查询结果可能很有用：
* 分页浏览查询结果。 初始命令会运行查询并返回记录的第一“页”。
  以后的查询会引用其他“页”，无需重新运行查询。
* 向下钻取方案。在此类方案中，初始查询的结果随后可供其他查询浏览。

> [!NOTE]
> * 存储的查询结果处于预览阶段，不应该用于生产方案。 
> * 只有在启用 [EngineV3](../../engine-v3.md) 的情况下，此功能才可用。

存储的查询结果从创建的那一刻起最多可供访问 24 小时。 对安全策略（例如，数据库访问、行级别安全性等）的更新不会传播到存储的查询结果。 如果存在用户权限吊销的情况，请使用 [`.drop stored_query_results`](#drop-stored_query_results)。 只可通过创建了存储的查询的同一主体标识访问存储的查询结果。 

存储的查询结果的行为类似于表，因为记录的顺序不会保留。 若要以分页的形式浏览结果，建议让查询包含独一无二的 ID 列。 有关详细信息，请参阅[示例](#examples)。 如果查询返回了多个结果集，则只会存储第一个结果集。 

使用存储的查询结果需要“`Database Viewer`”或更高级别的访问角色。

## <a name="store-the-results-of-a-query"></a>存储查询结果

**语法**

`.set` `stored_query_result` *StoredQueryResultName* [`with` `(`*PropertyName* `=` *PropertyValue* `,` ... `)`] <| *Query*

**参数**

* StoredQueryResultName：遵循实体名称规则的存储查询结果名称。[](../query/schema-entities/entity-names.md)
* *查询*：可能为重型的 KQL 查询，其结果会被存储。
* PropertyName：（所有属性都是可选的）
    
    | properties       | 类型       | 说明       |
    |----------------|------------|-------------------------------------------------------------------------------------|
    | `expiresAfter` | `timespan` | 指示存储的查询结果会在何时过期的时间跨度文本（最长时间为 24 小时）。 |
    | `previewCount` | `int`      | 要在预览中返回的行数。 如果将此属性设置为 `0`（默认值），则该命令会返回所有查询结果行。 |
    | `distributed`  | `bool`     | 指示此命令存储来自以并行方式执行查询的所有节点的查询结果。 默认值为 true。 如果查询生成的数据量较小，或者群集节点数较大，则将 `distributed` 标志设置为 false 非常有用，可避免创建很多小型数据分片。 |

## <a name="retrieve-a-stored-query-result"></a>检索存储的查询结果

若要检索存储的查询结果，请在查询中使用 `stored_query_result()` 函数：

`stored_query_result` `(` 'StoredQueryResultName' `)` `|` ...

## <a name="examples"></a>示例

### <a name="simple-query"></a>简单查询

存储简单查询结果：

<!-- csl -->
```kusto
.set stored_query_result Numbers <| range X from 1 to 1000000 step 1
```

**输出：**

| X |
|---|
| 1 |
| 2 |
| 3 |
| ... |

检索存储的查询结果：

<!-- csl -->
```kusto
stored_query_result("Numbers")
```

**输出：**

| X |
|---|
| 1 |
| 2 |
| 3 |
| ... |

### <a name="pagination"></a>分页

按广告网络和日期检索过去七天内的点击数：

<!-- csl -->
```kusto
.set stored_query_result DailyClicksByAdNetwork7Days with (previewCount = 100) <|
Events
| where Timestamp > ago(7d)
| where EventType == 'click'
| summarize Count=count() by Day=bin(Timestamp, 1d), AdNetwork
| order by Count desc
| project Num=row_number(), Day, AdNetwork, Count
```

**输出：**

| 编号 | 天 | AdNetwork | 计数 |
|-----|-----|-----------|-------|
| 1 | 2020-01-01 00:00:00.0000000 | NeoAds | 1002 |
| 2 | 2020-01-01 00:00:00.0000000 | HighHorizons | 543 |
| 3 | 2020-01-01 00:00:00.0000000 | PieAds | 379 |
| ... | ... | ... | ... |

检索下一页：

<!-- csl -->
```kusto
stored_query_result("DailyClicksByAdNetwork7Days")
| where Num between(100 .. 200)
```

**输出：**

| 编号 | 天 | AdNetwork | 计数 |
|-----|-----|-----------|-------|
| 100 | 2020-01-01 00:00:00.0000000 | CoolAds | 301 |
| 101 | 2020-01-01 00:00:00.0000000 | DreamAds | 254 |
| 102 | 2020-01-02 00:00:00.0000000 | SuperAds | 123 |
| ... | ... | ... | ... |

## <a name="control-commands"></a>控制命令

### <a name="show-stored_query_results"></a>.show stored_query_results

显示有关活动存储查询结果的信息。

>[!NOTE]
> * 具有 `DatabaseAdmin` 或 `DatabaseMonitor` 权限的用户可以检查数据库上下文中是否存在活动存储查询结果。
> * 具有 `DatabaseUser` 或 `DatabaseViewer` 权限的用户可以检查是否存在由其主体创建的活动存储查询结果。

#### <a name="syntax"></a>语法

`.show` `stored_query_results`

#### <a name="returns"></a>返回

| StoredQueryResultId | 名称 | DatabaseName | PrincipalIdentity | SizeInBytes | RowCount | CreatedOn | ExpiresOn |
| ------------------- | ---- | ------------ | ----------------- | ----------- | -------- | --------- | --------- |
| c522ada3-e490-435a-a8b1-e10d00e7d5c2 | 事件 | TestDB | aadapp=c28e9b80-2808-bed525fc0fbb | 104372 | 1000000 | 2020-10-07 14:26:49.6971487 | 2020-10-08 14:26:49.6971487 |

### <a name="drop-stored_query_result"></a>.drop stored_query_result

删除由当前主体在当前数据库中创建的活动存储查询结果。

#### <a name="syntax"></a>语法

`.drop` `stored_query_result` *StoredQueryResultName*

需要有“`Database Viewer`”权限才能调用此命令。

#### <a name="returns"></a>返回

返回已删除的存储查询结果的相关信息，例如：

| StoredQueryResultId | 名称 | DatabaseName | PrincipalIdentity | SizeInBytes | RowCount | CreatedOn | ExpiresOn |
| ------------------- | ---- | ------------ | ----------------- | ----------- | -------- | --------- | --------- |
| c522ada3-e490-435a-a8b1-e10d00e7d5c2 | 事件 | TestDB | aadapp=c28e9b80-2808-bed525fc0fbb | 104372 | 1000000 | 2020-10-07 14:26:49.6971487 | 2020-10-08 14:26:49.6971487 |

### <a name="drop-stored_query_results"></a>.drop stored_query_results

删除由指定主体在当前数据库中创建的活动存储查询结果。

需要有“`Database Admin`”权限才能调用此命令。

#### <a name="syntax"></a>语法

`.drop` `stored_query_results` `created-by` *PrincipalName*

#### <a name="returns"></a>返回

返回已删除的存储查询结果的相关信息。

示例：

<!-- csl -->
```kusto
.drop stored_query_results by user 'aadapp=c28e9b80-2808-bed525fc0fbb'
```

| StoredQueryResultId | 名称 | DatabaseName | PrincipalIdentity | SizeInBytes | RowCount | CreatedOn | ExpiresOn |
| ------------------- | ---- | ------------ | ----------------- | ----------- | -------- | --------- | --------- |
| c522ada3-e490-435a-a8b1-e10d00e7d5c2 | 事件 | TestDB | aadapp=c28e9b80-2808-bed525fc0fbb | 104372 | 1000000 | 2020-10-07 14:26:49.6971487 | 2020-10-08 14:26:49.6971487 |
| 571f1a76-f5a9-49d4-b339-ba7caac19b46 | 跟踪 | TestDB | aadapp=c28e9b80-2808-bed525fc0fbb | 5212 | 100000 | 2020-10-07 14:31:01.8271231| 2020-10-08 14:31:01.8271231 |
