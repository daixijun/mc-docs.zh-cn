---
title: series_downsample_fl() - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的用户定义函数 series_downsample_fl()。
author: orspod
ms.author: v-tawe
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
origin.date: 11/25/2020
ms.date: 01/22/2021
ms.openlocfilehash: 776f31c5751ea8fc3a2acd41fb3a98249a32faaf
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614874"
---
# <a name="series_downsample_fl"></a>series_downsample_fl()


函数 `series_downsample_fl()` [按整数因子对时序减少采样](https://en.wikipedia.org/wiki/Downsampling_(signal_processing)#Downsampling_by_an_integer_factor)。 此函数采用包含多个时序（动态数值阵列）的表，并对每个序列减少采样。 输出包含较粗的序列及其各自的时间数组。 为了避免[别名](https://en.wikipedia.org/wiki/Aliasing)，该函数会在子采样之前对每个序列都应用简单的[低通筛选器](https://en.wikipedia.org/wiki/Low-pass_filter)。

> [!NOTE]
> 此函数是 [UDF（用户定义的函数）](../query/functions/user-defined-functions.md)。 有关详细信息，请参阅[用法](#usage)。

## <a name="syntax"></a>语法

`T | invoke series_downsample_fl(`*t_col*`,` *y_col*`,` *ds_t_col*`,` *ds_y_col*`,` *sampling_factor*`)`

## <a name="arguments"></a>参数

* t_col：输出表的列（其中包含要减少采样的序列的时间轴）的名称。
* y_col：输出表的列（其中包含要减少采样的序列）的名称。
* ds_t_col：列（用于存储每个序列的已减少采样的时间轴）的名称。
* ds_y_col：列（用于存储已减少采样的序列）的名称。
* sampling_factor：一个整数，指定所需要的减少采样程度。

## <a name="usage"></a>使用情况

`series_downsample_fl()` 是用户定义的[表格函数](../query/functions/user-defined-functions.md#tabular-function)，需使用 [invoke 运算符](../query/invokeoperator.md)进行应用。 可以在查询中嵌入其代码，或将其安装在数据库中。 用法选项有两种：临时使用和永久使用。 有关示例，请参阅下面的选项卡。

# <a name="ad-hoc"></a>[临时](#tab/adhoc)

如果是临时使用，请使用 [let 语句](../query/letstatement.md)嵌入该函数的代码。 不需要权限。

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
let series_downsample_fl=(tbl:(*), t_col:string, y_col:string, ds_t_col:string, ds_y_col:string, sampling_factor:int)
{
    tbl
    | extend _t_ = column_ifexists(t_col, dynamic(0)), _y_ = column_ifexists(y_col, dynamic(0))
    | extend _y_ = series_fir(_y_, repeat(1, sampling_factor), true, true)    //  apply a simple low pass filter before sub-sampling
    | mv-apply _t_ to typeof(DateTime), _y_ to typeof(double) on
    (extend rid=row_number()
    | where rid % sampling_factor == (sampling_factor/2+1)                    //  sub-sampling
    | summarize _t_ = make_list(_t_), _y_ = make_list(_y_))
    | extend cols = pack(ds_t_col, _t_, ds_y_col, _y_)
    | project-away _t_, _y_
    | evaluate bag_unpack(cols)
}
;
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| invoke series_downsample_fl('TimeStamp', 'num', 'coarse_TimeStamp', 'coarse_num', 4)
| render timechart with(xcolumn=coarse_TimeStamp, ycolumns=coarse_num)
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

如果是永久使用，请使用 [`.create function`](../management/create-function.md)。 创建函数需要有[数据库用户权限](../management/access-control/role-based-authorization.md)。

### <a name="one-time-installation"></a>一次性安装

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Downsampling a series by an integer factor")
series_downsample_fl(tbl:(*), t_col:string, y_col:string, ds_t_col:string, ds_y_col:string, sampling_factor:int)
{
    tbl
    | extend _t_ = column_ifexists(t_col, dynamic(0)), _y_ = column_ifexists(y_col, dynamic(0))
    | extend _y_ = series_fir(_y_, repeat(1, sampling_factor), true, true)    //  apply a simple low pass filter before sub-sampling
    | mv-apply _t_ to typeof(DateTime), _y_ to typeof(double) on
    (extend rid=row_number()
    | where rid % sampling_factor == (sampling_factor/2+1)                    //  sub-sampling
    | summarize _t_ = make_list(_t_), _y_ = make_list(_y_))
    | extend cols = pack(ds_t_col, _t_, ds_y_col, _y_)
    | project-away _t_, _y_
    | evaluate bag_unpack(cols)
}
```

### <a name="usage"></a>使用情况

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| invoke series_downsample_fl('TimeStamp', 'num', 'coarse_TimeStamp', 'coarse_num', 4)
| render timechart with(xcolumn=coarse_TimeStamp, ycolumns=coarse_num)
```

---

该时序减少采样的程度为 4：:::image type="content" source="images/series-downsample-fl/downsampling-demo.png" alt-text="此图显示时序减少采样" border="false":::

下面是原始时序（在减少采样之前），供参考：
<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| render timechart with(xcolumn=TimeStamp, ycolumns=num)
```

:::image type="content" source="images/series-downsample-fl/original-time-series.png" alt-text="此图显示在减少采样之前的原始时序" border="false":::

