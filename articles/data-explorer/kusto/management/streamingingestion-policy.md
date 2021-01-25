---
title: Kusto 流式引入策略管理 - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的流式引入策略管理。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/24/2020
ms.date: 01/22/2021
ms.openlocfilehash: 1bdea8c8d920a2dbdb99c40a0d4142400a513b23
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611480"
---
# <a name="streaming-ingestion-policy-command"></a>流式引入策略命令

可以对表设置流式引入策略，以允许流式引入到此表中。 也可以在数据库级别设置策略，以便将相同的设置应用于当前表和未来的表。

有关详细信息，请参阅[流式引入](../../ingest-data-streaming.md)。 若要了解有关流式引入策略的详细信息，请参阅[流式引入策略](streamingingestionpolicy.md)。

## <a name="display-the-policy"></a>显示策略

`.show policy streamingingestion` 命令显示数据库或表的流式引入策略。
 
**语法**

`.show` `{database|table}` &lt;entity name&gt; `policy` `streamingingestion`

**返回**

此命令返回包含以下列的表：

|列    |类型    |说明
|---|---|---
|PolicyName|`string`|策略名称 - StreamingIngestionPolicy
|EntityName|`string`|数据库或表名称
|策略    |`string`|[流式引入策略对象](#streaming-ingestion-policy-object)

**示例**

```kusto
.show database DB1 policy streamingingestion

.show table T1 policy streamingingestion
```

|PolicyName|EntityName|策略|ChildEntities|EntityType|
|---|---|---|---|---|
|StreamingIngestionPolicy|DB1|{"IsEnabled": true, "HintAllocatedRate": null}

## <a name="change-the-policy"></a>更改策略

`.alter[-merge] policy streamingingestion` 系列命令提供修改数据库或表的流式引入策略的方法。

**语法**

* `.alter` `{database|table}` &lt;entity name&gt; `policy` `streamingingestion` `[enable|disable]`

* `.alter` `{database|table}` &lt;entity name&gt; `policy` `streamingingestion` &lt;[streaming ingestion policy object](#streaming-ingestion-policy-object)&gt;

* `.alter-merge` `{database|table}` &lt;entity name&gt; `policy` `streamingingestion` &lt;[streaming ingestion policy object](#streaming-ingestion-policy-object)&gt;

> [!Note]
>
> * 如果先前未在实体上定义策略，则无需更改策略的其他属性或将属性设置为默认值，即可更改流式引入的启用/禁用状态。
>
> * 允许替换实体上的整个流式引入策略。 [流式引入策略对象](#streaming-ingestion-policy-object)必须包含所有必需的属性。
>
> * 允许只替换实体上流式引入策略的指定属性。 [流式引入策略对象](#streaming-ingestion-policy-object)可以包含部分或不包含强制性属性。

**返回**

此命令修改表或数据库 `streamingingestion` 策略对象，然后返回相应的 [`.show policy` `streamingingestion`](#display-the-policy) 命令的输出。

**示例**

```kusto
.alter database DB1 policy streamingingestion enable

.alter table T1 policy streamingingestion disable

.alter database DB1 policy streamingingestion '{"IsEnabled": true, "HintAllocatedRate": 2.1}'

.alter table T1 policy streamingingestion '{"IsEnabled": true}'

.alter-merge database DB1 policy streamingingestion '{"IsEnabled": false}'

.alter-merge table T1 policy streamingingestion '{"HintAllocatedRate": 1.5}'
```

## <a name="delete-the-policy"></a>删除策略

`.delete policy streamingingestion` 命令从数据库或表中删除流式引入策略。

**语法**

`.delete` `{database|table}` &lt;entity name&gt; `policy` `streamingingestion`

**返回**

此命令删除表或数据库 streamingestion 策略对象，然后返回相应的 [`.show policy streamingingestion`](#display-the-policy) 命令的输出。

**示例**

```kusto
.delete database DB1 policy streamingingestion

.delete table T1 policy streamingingestion
```

### <a name="streaming-ingestion-policy-object"></a>流式引入策略对象

在管理命令的输入和输出中，流式引入策略对象是 JSON 格式的字符串，其中包括以下属性。

|属性|类型|说明|必需/可选
|---|---|---|---
|IsEnabled|`bool`|实体是否启用了流式引入| 必须
|HintAllocatedRate|`double`|数据流入量的估计速率（Gb/小时）|可选
