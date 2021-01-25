---
title: 适用于 Azure 数据资源管理器和 Azure Monitor 的 Splunk 到 Kusto 映射
description: 通过概念映射方便那些熟悉 Splunk 的用户学习 Kusto 查询语言来编写日志查询。
ms.service: data-explorer
ms.topic: conceptual
author: bwren
ms.author: v-tawe
origin.date: 08/21/2018
ms.date: 01/22/2021
ms.openlocfilehash: 9bd8b37f1b30f630f57c2769f4712a8aab4ad82f
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614919"
---
# <a name="splunk-to-kusto-query-language-map"></a>Splunk 到 Kusto 查询语言映射

本文旨在帮助熟悉 Splunk 的用户学习 Kusto 查询语言，以便使用 Kusto 编写日志查询。 本文对二者进行了直接比较，以便强调它们的主要差异和相似之处，从而方便你在现有知识的基础上进行积累。

## <a name="structure-and-concepts"></a>结构和概念

下表比较了 Splunk 和 Kusto 日志的概念与数据结构：

 | 概念 | Splunk | Kusto |  评论 |
 |:---|:---|:---|:---|
 | 部署单元  | cluster |  cluster |  Kusto 允许跨群集进行任意查询， Splunk 则不允许。 |
 | 数据缓存 |  存储桶  |  缓存和保留策略 |  控制数据的保留期和缓存级别。 此设置直接影响查询性能和部署成本。 |
 | 数据的逻辑分区  |  索引  |  database  |  允许数据的逻辑隔离。 这两个实现都允许跨这些分区的联合与联接。 |
 | 结构化事件元数据 | 空值 | 表 |  Splunk 没有将事件元数据的概念公开给搜索语言。 Kusto 日志有表的概念，表包含列。 每个事件实例映射到行。 |
 | 数据记录 | event | 行 |  仅限术语变化。 |
 | 数据记录属性 | 字段 |  列 |  在 Kusto 中，此设置预定义为表结构的一部分。 在 Splunk 中，每个事件有自身的字段集。 |
 | types | 数据类型 |  数据类型 |  Kusto 数据类型更明确，因为它们是在列中设置的。 两者都能动态处理数据类型，并且两者的数据类型集（包括 JSON 支持）大致相同。 |
 | 查询和搜索  | 搜索 | query |  概念在 Kusto 和 Splunk 之间在本质上相同。 |
 | 事件引入时间 | 系统时间 | `ingestion_time()` |  在 Splunk 中，每个事件都会获取编制事件索引时的系统时间戳。 在 Kusto 中，可以定义名为 [ingestion_time](../management/ingestiontimepolicy.md) 的策略，用于公开可通过 [ingestion_time()](ingestiontimefunction.md) 函数引用的系统列。 |

## <a name="functions"></a>函数

下表指定了 Kusto 中等效于 Splunk 函数的函数。

|Splunk | Kusto |评论
|:---|:---|:---
| `strcat` | `strcat()` | (1) |
| `split`  | `split()` | (1) |
| `if`     | `iff()`   | (1) |
| `tonumber` | `todouble()`<br />`tolong()`<br />`toint()` | (1) |
| `upper`<br />`lower` |`toupper()`<br />`tolower()`|(1) |
| `replace` | `replace()` | (1)<br /> 另请注意，尽管这两个产品中的 `replace()` 都采用三个参数，但这些参数不同。 |
| `substr` | `substring()` | (1)<br />另请注意，Splunk 使用从 1 开始的索引。 Kusto 记录从 0 开始的索引。 |
| `tolower` |  `tolower()` | (1) |
| `toupper` | `toupper()` | (1) |
| `match` | `matches regex` |  (2)  |
| `regex` | `matches regex` | 在 Splunk 中，`regex` 是运算符。 在 Kusto 中，它是关系运算符。 |
| `searchmatch` | == | 在 Splunk 中，`searchmatch` 允许搜索确切的字符串。
| `random` | rand()<br />rand(n) | Splunk 的函数会返回 0 到 2<sup>31</sup>-1 之间的数字。 Kusto 的函数会返回 0.0 到 1.0 之间的数字；如果提供了参数，则会返回 0 到 n-1 之间的数字。
| `now` | `now()` | (1)
| `relative_time` | `totimespan()` | (1)<br />在 Kusto 中，Splunk 的 `relative_time(datetimeVal, offsetVal)` 等效项为 `datetimeVal + totimespan(offsetVal)`。<br />例如，`search` &#124; `eval n=relative_time(now(), "-1d@d")` 变为 `...` &#124; `extend myTime = now() - totimespan("1d")`。

(1) 在 Splunk 中，使用 `eval` 运算符调用该函数。 在 Kusto 中，它用作 `extend` 或 `project` 的一部分。<br />(2) 在 Splunk 中，使用 `eval` 运算符调用该函数。 在 Kusto 中，该函数可以与 `where` 运算符配合使用。


## <a name="operators"></a>运算符

以下部分通过示例演示如何在 Splunk 和 Kusto 中使用不同的运算符。

> [!NOTE]
> 在以下示例中，Splunk 字段 `rule` 映射到 Kusto 中的某个表，Splunk 的默认时间戳映射到 Logs Analytics 的 `ingestion_time()` 列。

### <a name="search"></a>搜索

在 Splunk 中，可以省略 `search` 关键字，并指定不带引号的字符串。 在 Kusto 中，每个查询必须以 `find` 开头，不带引号的字符串是列名，查找值必须是带引号的字符串。 

| 产品 | 运算符 | 示例 |
|:---|:---|:---|
| Splunk | `search` | `search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h` |
| Kusto | `find` | `find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)` |


### <a name="filter"></a>筛选器

Kusto 日志查询从应用了 `filter` 的表格结果集开始。 在 Splunk 中，筛选是针对当前索引执行的默认操作。 你还可以在 Splunk 中使用 `where` 运算符，但我们不建议那样做。

| 产品 | 运算符 | 示例 |
|:---|:---|:---|
| Splunk | `search` | `Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h` |
| Kusto | `where` | `Office_Hub_OHubBGTaskError`<br />&#124; `where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)` |

### <a name="get-n-events-or-rows-for-inspection"></a>获取要检查的 n 个事件或行

Kusto 日志查询还支持将 `take` 用作 `limit` 的别名。 在 Splunk 中，如果结果已排序，则 `head` 会返回前 n 个结果。 在 Kusto 中，`limit` 不会排序，而是返回找到的前 n 行。

| 产品 | 运算符 | 示例 |
|:---|:---|:---|
| Splunk | `head` | `Event.Rule=330009.2`<br />&#124; `head 100` |
| Kusto | `limit` | `Office_Hub_OHubBGTaskError`<br />&#124; `limit 100` |

### <a name="get-the-first-n-events-or-rows-ordered-by-a-field-or-column"></a>获取按字段或列排序的前 n 个事件或行

对于底部结果，在 Splunk 中可以使用 `tail`。 在 Kusto 中，可以使用 `asc` 指定排序方向。

| 产品 | 运算符 | 示例 |
|:---|:---|:---|
| Splunk | `head` |  `Event.Rule="330009.2"`<br />&#124; `sort Event.Sequence`<br />&#124; `head 20` |
| Kusto | `top` | `Office_Hub_OHubBGTaskError`<br />&#124; `top 20 by Event_Sequence` |

### <a name="extend-the-result-set-with-new-fields-or-columns"></a>使用新字段或列扩展结果集

Splunk 有一个 `eval` 函数，但该函数不可与 Kusto 中的 `eval` 运算符相比。 Splunk 中的 `eval` 运算符与 Kusto 中的 `extend` 运算符均仅支持标量函数和算术运算符。

| 产品 | 运算符 | 示例 |
|:---|:---|:---|
| Splunk | `eval` |  `Event.Rule=330009.2`<br />&#124; `eval state= if(Data.Exception = "0", "success", "error")` |
| Kusto | `extend` | `Office_Hub_OHubBGTaskError`<br />&#124; `extend state = iif(Data_Exception == 0,"success" ,"error")` |

### <a name="rename"></a>重命名

Kusto 使用 `project-rename` 运算符来重命名字段。 在 `project-rename` 运算符中，查询可以利用为字段预先生成的任何索引。 Splunk 提供的 `rename` 运算符也有相同的作用。

| 产品 | 运算符 | 示例 |
|:---|:---|:---|
| Splunk | `rename` |  `Event.Rule=330009.2`<br />&#124; `rename Date.Exception as execption` |
| Kusto | `project-rename` | `Office_Hub_OHubBGTaskError`<br />&#124; `project-rename exception = Date_Exception` |

### <a name="format-results-and-projection"></a>设置结果和投影的格式

Splunk 似乎没有类似于 `project-away` 的运算符。 可以使用 UI 将字段筛选掉。

| 产品 | 运算符 | 示例 |
|:---|:---|:---|
| Splunk | `table` |  `Event.Rule=330009.2`<br />&#124; `table rule, state` |
| Kusto | `project`<br />`project-away` | `Office_Hub_OHubBGTaskError`<br />&#124; `project exception, state` |

### <a name="aggregation"></a>聚合

请参阅可用的[聚合函数的列表](summarizeoperator.md#list-of-aggregation-functions)。

| 产品 | 运算符 | 示例 |
|:---|:---|:---|
| Splunk | `stats` |  `search (Rule=120502.*)`<br />&#124; `stats count by OSEnv, Audience` |
| Kusto | `summarize` | `Office_Hub_OHubBGTaskError`<br />&#124; `summarize count() by App_Platform, Release_Audience` |


### <a name="join"></a>联接

Splunk 中的 `join` 具有实质性的限制。 子查询限制为 10,000 条结果（在部署配置文件中设置），联接风格数目也有限制。

| 产品 | 运算符 | 示例 |
|:---|:---|:---|
| Splunk | `join` |  `Event.Rule=120103* &#124; stats by Client.Id, Data.Alias` <br />&#124; `join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]` |
| Kusto | `join` | `cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions`<br />&#124; `where  Data_Hresult== -2147221040`<br />&#124; `join kind = inner (Office_System_SystemHealthMetadata`<br />&#124; `summarize by Client_Id, Data_Alias)on Client_Id`   |

### <a name="sort"></a>排序

在 Splunk 中，若要按升序排序，必须使用 `reverse` 运算符。 Kusto 还支持定义 null 值的放置位置：开头或末尾。

| 产品 | 运算符 | 示例 |
|:---|:---|:---|
| Splunk | `sort` |  `Event.Rule=120103`<br />&#124; `sort Data.Hresult` <br />&#124; `reverse` |
| Kusto | `order by` | `Office_Hub_OHubBGTaskError`<br />&#124; `order by Data_Hresult,  desc` |

### <a name="multivalue-expand"></a>多值扩展

多值扩展运算符在 Splunk 和 Kusto 中类似。

| 产品 | 运算符 | 示例 |
|:---|:---|:---|
| Splunk | `mvexpand` |  `mvexpand solutions` |
| Kusto | `mv-expand` | `mv-expand solutions` |

### <a name="result-facets-interesting-fields"></a>结果 facet、相关字段

在 Azure 门户的 Log Analytics 中，仅公开第一列。 可通过 API 查看所有列。

| 产品 | 运算符 | 示例 |
|:---|:---|:---|
| Splunk | `fields` |  `Event.Rule=330009.2`<br />&#124; `fields App.Version, App.Platform` |
| Kusto | `facets` | `Office_Excel_BI_PivotTableCreate`<br />&#124; `facet by App_Branch, App_Version` |

### <a name="deduplicate"></a>删除重复数据

在 Kusto 中，可以使用 `summarize arg_min()` 来反转记录的选择顺序。

| 产品 | 运算符 | 示例 |
|:---|:---|:---|
| Splunk | `dedup` |  `Event.Rule=330009.2`<br />&#124; `dedup device_id sortby -batterylife` |
| Kusto | `summarize arg_max()` | `Office_Excel_BI_PivotTableCreate`<br />&#124; `summarize arg_max(batterylife, *) by device_id` |

## <a name="next-steps"></a>后续步骤

- 演练 [Kusto 查询语言](tutorial.md?pivots=azuremonitor)教程。
