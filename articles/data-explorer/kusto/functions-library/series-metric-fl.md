---
title: series_metric_fl() - Azure 数据资源管理器
description: 本文介绍了 Azure 数据资源管理器中用户定义的函数 series_metric_fl()。
author: orspod
ms.author: v-tawe
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
origin.date: 12/13/2020
ms.date: 01/22/2021
ms.openlocfilehash: b90258a97e906a15e052a52be3c31af8e5abb1a9
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614892"
---
# <a name="series_metric_fl"></a>series_metric_fl()


`series_metric_fl()` 函数可选择并检索通过 [Prometheus](https://prometheus.io/) 监视系统引入到 Azure 数据资源管理器的指标的时序。 此函数假定存储在 Azure 数据资源管理器中的数据是按照 [Prometheus 数据模型](https://prometheus.io/docs/concepts/data_model/)进行了结构化的数据。 具体而言，每个记录都包含：
 * timestamp 
 * 指标名称 
 * 指标值 
 * 一组可变的标签（`"key":"value"` 对）
 
 Prometheus 按其指标名称和一组非重复的标签来定义时序。 你可以使用 [Prometheus 查询语言 (PromQL)](https://prometheus.io/docs/prometheus/latest/querying/basics/)，通过指定指标名称和时序选择器（一组标签）来检索时序集。

> [!NOTE]
> `series_metric_fl()` 是 [UDF（用户定义的函数）](../query/functions/user-defined-functions.md)。 有关详细信息，请参阅[用法](#usage)。

## <a name="syntax"></a>语法

`T | invoke series_metric_fl(`*timestamp_col*`,` *name_col*`,` *labels_col*`,` *value_col*`,` *metric_name*`,` *labels_selector*`,` *lookback*`,` *offset*`)`

## <a name="arguments"></a>参数

* timestamp_col：包含时间戳的列的名称。
* name_col：包含指标名称的列的名称。
* labels_col：包含标签字典的列的名称。
* value_col：包含指标值的列的名称。
* metric_name：要检索的指标时序。
* labels_selector：时序选择器字符串，[类似于 PromQL](https://prometheus.io/docs/prometheus/latest/querying/basics/#time-series-selectors)。 它是一个包含 `"key":"value"` 对列表的字符串，例如 `'"key1":"val1","key2":"val2"'`。 此参数是可选的，默认为空字符串，这意味着不进行筛选。 请注意，不支持正则表达式。 
* lookback：要检索的范围向量，[类似于 PromQL](https://prometheus.io/docs/prometheus/latest/querying/basics/#range-vector-selectors)。 此参数是可选的，默认为 10 分钟。
* *offset*：要检索的偏移量（相对于当前时间更早），[类似于 PromQL](https://prometheus.io/docs/prometheus/latest/querying/basics/#offset-modifier)。 数据是从 ago(offset)-lookback 到 ago(offset) 的范围检索的。 此参数是可选的，默认为 0，这意味着数据会检索到 now() 为止。

## <a name="usage"></a>使用情况

`series_metric_fl()` 是用户定义的[表格函数](../query/functions/user-defined-functions.md#tabular-function)，需使用 [invoke 运算符](../query/invokeoperator.md)进行应用。 可以在查询中嵌入其代码，或将其安装在数据库中。 用法选项有两种：临时使用和永久使用。 有关示例，请参阅下面的选项卡。

# <a name="ad-hoc"></a>[临时](#tab/adhoc)

如果是临时使用，请使用 [let 语句](../query/letstatement.md)嵌入其代码。 不需要权限。

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
let series_metric_fl=(metrics_tbl:(*), timestamp_col:string, name_col:string, labels_col:string, value_col:string, metric_name:string, labels_selector:string='', lookback:timespan=timespan(10m), offset:timespan=timespan(0))
{
    let selector_d=iff(labels_selector == '', dynamic(['']), split(labels_selector, ','));
    let etime = ago(offset);
    let stime = etime - lookback;
    metrics_tbl
    | extend timestamp = column_ifexists(timestamp_col, datetime(null)), name = column_ifexists(name_col, ''), labels = column_ifexists(labels_col, dynamic(null)), value = column_ifexists(value_col, 0)
    | extend labels = dynamic_to_json(labels)       //  convert to string and sort by key
    | where name == metric_name and timestamp between(stime..etime)
    | order by timestamp asc
    | summarize timestamp = make_list(timestamp), value=make_list(value) by name, labels
    //  KQL has native has_any(), but no native has_all(), the lines below implement has_all()
    | mv-apply x = selector_d to typeof(string) on (
      summarize countif(labels has x)
      | where countif_ == array_length(selector_d)
    )
    | project-away countif_
}
;
//
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', '"disk":"sda1","host":"aks-agentpool-88086459-vmss000001"', offset=now()-datetime(2020-12-08 00:00))
| render timechart with(series=labels)
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

如果是永久使用，请使用 [`.create function`](../management/create-function.md)。 创建函数需要有[数据库用户权限](../management/access-control/role-based-authorization.md)。

### <a name="one-time-installation"></a>一次性安装

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
.create function with (folder = "Packages\\Series", docstring = "Selecting & retrieving metrics like PromQL")
series_metric_fl(metrics_tbl:(*), timestamp_col:string, name_col:string, labels_col:string, value_col:string, metric_name:string, labels_selector:string='', lookback:timespan=timespan(10m), offset:timespan=timespan(0))
{
    let selector_d=iff(labels_selector == '', dynamic(['']), split(labels_selector, ','));
    let etime = ago(offset);
    let stime = etime - lookback;
    metrics_tbl
    | extend timestamp = column_ifexists(timestamp_col, datetime(null)), name = column_ifexists(name_col, ''), labels = column_ifexists(labels_col, dynamic(null)), value = column_ifexists(value_col, 0)
    | extend labels = dynamic_to_json(labels)       //  convert to string and sort by key
    | where name == metric_name and timestamp between(stime..etime)
    | order by timestamp asc
    | summarize timestamp = make_list(timestamp), value=make_list(value) by name, labels
    //  KQL has native has_any(), but no native has_all(), the lines below implement has_all()
    | mv-apply x = selector_d to typeof(string) on (
      summarize countif(labels has x)
      | where countif_ == array_length(selector_d)
    )
    | project-away countif_
}
```

### <a name="usage"></a>使用情况

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', '"disk":"sda1","host":"aks-agentpool-88086459-vmss000001"', offset=now()-datetime(2020-12-08 00:00))
| render timechart with(series=labels)
```

---

:::image type="content" source="images/series-metric-fl/disk-write-metric-10m.png" alt-text="此图显示 10 分钟内的磁盘写入指标" border="false":::

## <a name="example"></a>示例

以下示例未指定选择器，因此选择了所有 'writes' 指标。 此示例假定此函数已安装并使用另一种可供选择的直接调用语法，将输入表指定为第一个参数：
    
<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
series_metric_fl(demo_prometheus, 'TimeStamp', 'Name', 'Labels', 'Val', 'writes', offset=now()-datetime(2020-12-08 00:00))
| render timechart with(series=labels, ysplit=axes)
```
    
:::image type="content" source="images/series-metric-fl/all-disks-write-metric-10m.png" alt-text="此图显示 10 分钟内所有磁盘的磁盘写入指标" border="false":::
