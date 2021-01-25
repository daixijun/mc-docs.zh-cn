---
title: series_rate_fl() - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的用户定义函数 series_rate_fl()。
author: orspod
ms.author: v-tawe
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
origin.date: 12/13/2020
ms.date: 01/22/2021
ms.openlocfilehash: f1e00d0348fb52f7bd713e1142f341ecb9f46e8e
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614917"
---
# <a name="series_rate_fl"></a>series_rate_fl()


函数 `series_rate_fl()` 计算每秒计数器指标提高的平均速率。 其逻辑遵循 PromQL [rate()](https://prometheus.io/docs/prometheus/latest/querying/functions/#rate) 函数。 该函数应该用于由 [Prometheus](https://prometheus.io/) 监视系统引入到 Azure 数据资源管理器并由 [series_metric_fl()](series-metric-fl.md) 检索的计数器指标的时序。

> [!NOTE]
>`series_rate_fl()` 是 [UDF（用户定义的函数）](../query/functions/user-defined-functions.md)。 有关详细信息，请参阅[用法](#usage)。

## <a name="syntax"></a>语法

`T | invoke series_rate_fl(`n_bins`)`

`T` 是从 [series_metric_fl()](series-metric-fl.md) 返回的表。 其架构包括 `(timestamp:dynamic, name:string, labels:string, value:dynamic)`。

## <a name="arguments"></a>参数

* n_bins：箱的数量，用于指定为计算速率而提取的指标值之间的差距。 该函数计算当前样本与前一个 n_bins 之差，并将此差值除以二者各自时间戳（以秒为单位）之差。 此参数是可选的，默认值为一个箱。 该默认设置计算 PromQL 瞬时速率函数 [irate()](https://prometheus.io/docs/prometheus/latest/querying/functions/#irate)。
* fix_reset：一个布尔标志，控制是否检查计数器重置以及是否像 PromQL [rate()](https://prometheus.io/docs/prometheus/latest/querying/functions/#rate) 函数一样对计数器进行更正。 此参数是可选的，默认值为 `true`。 请将此参数设置为 `false`，以节省冗余分析，这样是为了应对无需检查重置的情况。

## <a name="usage"></a>使用情况

`series_rate_fl()` 是用户定义的[表格函数](../query/functions/user-defined-functions.md#tabular-function)，需使用 [invoke 运算符](../query/invokeoperator.md)进行应用。 可以在查询中嵌入其代码，或将其安装在数据库中。 用法选项有两种：临时使用和永久使用。 有关示例，请参阅下面的选项卡。

> [!NOTE]
> [series_metric_fl()](series-metric-fl.md) 用作该函数的一部分，并且必须安装或嵌入。

# <a name="ad-hoc"></a>[临时](#tab/adhoc)

如果是临时使用，请使用 [let 语句](../query/letstatement.md)嵌入其代码。 不需要权限。

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
let series_rate_fl=(tbl:(timestamp:dynamic, value:dynamic), n_bins:int=1, fix_reset:bool=true)
{
    tbl
    | where fix_reset                                                   //  Prometheus counters can only go up
    | mv-apply value to typeof(double) on   
    ( extend correction = iff(value < prev(value), prev(value), 0.0)    // if the value decreases we assume it was reset to 0, so add last value
    | extend cum_correction = row_cumsum(correction)
    | extend corrected_value = value + cum_correction
    | summarize value = make_list(corrected_value))
    | union (tbl | where not(fix_reset))
    | extend timestampS = array_shift_right(timestamp, n_bins), valueS = array_shift_right(value, n_bins)
    | extend dt = series_subtract(timestamp, timestampS)
    | extend dt = series_divide(dt, 1e7)                              //  converts from ticks to seconds
    | extend dv = series_subtract(value, valueS)
    | extend rate = series_divide(dv, dt)
    | project-away dt, dv, timestampS, value, valueS
}
;
//
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', offset=now()-datetime(2020-12-08 00:00))
| invoke series_rate_fl(2)
| render timechart with(series=labels)
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

如果是永久使用，请使用 [`.create function`](../management/create-function.md)。 创建函数需要有[数据库用户权限](../management/access-control/role-based-authorization.md)。

### <a name="one-time-installation"></a>一次性安装

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
.create function with (folder = "Packages\\Series", docstring = "Simulate PromQL rate()")
series_rate_fl(tbl:(timestamp:dynamic, value:dynamic), n_bins:int=1, fix_reset:bool=true)
{
    tbl
    | where fix_reset                                                   //  Prometheus counters can only go up
    | mv-apply value to typeof(double) on   
    ( extend correction = iff(value < prev(value), prev(value), 0.0)    // if the value decreases we assume it was reset to 0, so add last value
    | extend cum_correction = row_cumsum(correction)
    | extend corrected_value = value + cum_correction
    | summarize value = make_list(corrected_value))
    | union (tbl | where not(fix_reset))
    | extend timestampS = array_shift_right(timestamp, n_bins), valueS = array_shift_right(value, n_bins)
    | extend dt = series_subtract(timestamp, timestampS)
    | extend dt = series_divide(dt, 1e7)                              //  converts from ticks to seconds
    | extend dv = series_subtract(value, valueS)
    | extend rate = series_divide(dv, dt)
    | project-away dt, dv, timestampS, value, valueS
}
```

### <a name="usage"></a>使用情况

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', offset=now()-datetime(2020-12-08 00:00))
| invoke series_rate_fl(2)
| render timechart with(series=labels)
```

---

:::image type="content" source="images/series-rate-fl/all-disks-write-rate-2-bins.png" alt-text="此图显示所有磁盘的磁盘写入指标的每秒速率" border="false":::

## <a name="example"></a>示例

下面的示例选择两个主机的主磁盘，并假定已安装该函数。 此示例使用替代的直接调用语法，将输入表指定为第一个参数：
    
<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
series_rate_fl(series_metric_fl(demo_prometheus, 'TimeStamp', 'Name', 'Labels', 'Val', 'writes', '"disk":"sda1"', lookback=2h, offset=now()-datetime(2020-12-08 00:00)), n_bins=10)
| render timechart with(series=labels)
```
    
:::image type="content" source="images/series-rate-fl/main-disks-write-rate-10-bins.png" alt-text="此图显示在过去两小时中主磁盘写入指标的每秒速率（差距为 10 个箱）" border="false":::
