---
title: series_fit_lowess_fl() - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的用户定义函数 series_fit_lowess_fl()。
author: orspod
ms.author: v-tawe
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
origin.date: 11/29/2020
ms.date: 01/22/2021
no-loc: LOWESS
ms.openlocfilehash: c3ff9fdf8f492193af69b1aae6af725c975c2eae
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614875"
---
# <a name="series_fit_lowess_fl"></a>series_fit_lowess_fl()

函数 `series_fit_lowess_fl()` 对序列应用 LOWESS 回归。 此函数采用包含多个序列（动态数值阵列）的表，并生成一条 LOWESS 曲线，它是原始序列的平滑化版本。

> [!NOTE]
> * `series_fit_lowess_fl()` 是 [UDF（用户定义的函数）](../query/functions/user-defined-functions.md)。
> * 此函数包含内联 Python，需要在群集上[启用 python() 插件](../query/pythonplugin.md#enable-the-plugin)。 有关详细信息，请参阅[用法](#usage)。

## <a name="syntax"></a>语法

`T | invoke series_fit_lowess_fl(`y_series`,` y_fit_series`, [`fit_size`, `x_series`,` x_istime]`)`    

## <a name="arguments"></a>参数

* y_series：包含因变量的输入表列的名称。 此列是要拟合的序列。
* y_fit_series：用于存储已拟合序列的列的名称。
* fit_size：对于每个点，其各自的 fit_size 最近点会应用局部回归。 此参数是可选的，默认值为 5。
* x_series：包含自变量（即 x 轴或时间轴）的列的名称。 此参数为可选，只有[间距不均匀的序列](https://www.wikipedia.org/wiki/Unevenly_spaced_time_series)才需要。 默认值为空字符串，因为对于间距均匀的序列的回归，x 是冗余的。
* x_istime：仅当指定了 x_series 并且它是日期/时间的向量时，才需要此布尔参数。 此参数是可选的，默认值为 False。

## <a name="usage"></a>使用情况

`series_fit_lowess_fl()` 是用户定义的[表格函数](../query/functions/user-defined-functions.md#tabular-function)，需使用 [invoke 运算符](../query/invokeoperator.md)进行应用。 可以在查询中嵌入该函数的代码，或者在数据库中安装该函数。 用法选项有两种：临时使用和永久使用。 有关示例，请参阅下面的选项卡。

# <a name="ad-hoc"></a>[临时](#tab/adhoc)

如果是临时使用，请使用 [let 语句](../query/letstatement.md)嵌入该函数的代码。 不需要权限。

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
let series_fit_lowess_fl=(tbl:(*), y_series:string, y_fit_series:string, fit_size:int=5, x_series:string='', x_istime:bool=False)
{
    let kwargs = pack('y_series', y_series, 'y_fit_series', y_fit_series, 'fit_size', fit_size, 'x_series', x_series, 'x_istime', x_istime);
    let code=
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_fit_series = kargs["y_fit_series"]\n'
        'fit_size = kargs["fit_size"]\n'
        'x_series = kargs["x_series"]\n'
        'x_istime = kargs["x_istime"]\n'
        '\n'
        'import statsmodels.api as sm\n'
        'def lowess_fit(ts_row, x_col, y_col, fsize):\n'
        '    y = ts_row[y_col]\n'
        '    fraction = fsize/len(y)\n'
        '    if x_col == "": # If there is no x column creates sequential range [1, len(y)]\n'
        '       x = np.arange(len(y)) + 1\n'
        '    else: # if x column exists check whether its a time column. If so, normalize it to the [1, len(y)] range, else take it as is.\n'
        '       if x_istime: \n'
        '           x = pd.to_numeric(pd.to_datetime(ts_row[x_col]))\n'
        '           x = x - x.min()\n'
        '           x = x / x.max()\n'
        '           x = x * (len(x) - 1) + 1\n'
        '       else:\n'
        '           x = ts_row[x_col]\n'
        '    lowess = sm.nonparametric.lowess\n'
        '    z = lowess(y, x, return_sorted=False, frac=fraction)\n'
        '    return list(z)\n'
        '\n'
        'result = df\n'
        'result[y_fit_series] = df.apply(lowess_fit, axis=1, args=(x_series, y_series, fit_size))\n'
    ;
    tbl
     | evaluate python(typeof(*), code, kwargs)
};
//
// Apply 9 points LOWESS regression on regular time series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| make-series num=count() on TimeStamp from max_t-1d to max_t step 5m by OsVer
| extend fnum = dynamic(null)
| invoke series_fit_lowess_fl('num', 'fnum', 9)
| render timechart
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

如果是永久使用，请使用 [`.create function`](../management/create-function.md)。  创建函数需要有[数据库用户权限](../management/access-control/role-based-authorization.md)。

### <a name="one-time-installation"></a>一次性安装

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Fits a local polynomial using LOWESS method to a series")
series_fit_lowess_fl(tbl:(*), y_series:string, y_fit_series:string, fit_size:int=5, x_series:string='', x_istime:bool=False)
{
    let kwargs = pack('y_series', y_series, 'y_fit_series', y_fit_series, 'fit_size', fit_size, 'x_series', x_series, 'x_istime', x_istime);
    let code=
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_fit_series = kargs["y_fit_series"]\n'
        'fit_size = kargs["fit_size"]\n'
        'x_series = kargs["x_series"]\n'
        'x_istime = kargs["x_istime"]\n'
        '\n'
        'import statsmodels.api as sm\n'
        'def lowess_fit(ts_row, x_col, y_col, fsize):\n'
        '    y = ts_row[y_col]\n'
        '    fraction = fsize/len(y)\n'
        '    if x_col == "": # If there is no x column creates sequential range [1, len(y)]\n'
        '       x = np.arange(len(y)) + 1\n'
        '    else: # if x column exists check whether its a time column. If so, normalize it to the [1, len(y)] range, else take it as is.\n'
        '       if x_istime: \n'
        '           x = pd.to_numeric(pd.to_datetime(ts_row[x_col]))\n'
        '           x = x - x.min()\n'
        '           x = x / x.max()\n'
        '           x = x * (len(x) - 1) + 1\n'
        '       else:\n'
        '           x = ts_row[x_col]\n'
        '    lowess = sm.nonparametric.lowess\n'
        '    z = lowess(y, x, return_sorted=False, frac=fraction)\n'
        '    return list(z)\n'
        '\n'
        'result = df\n'
        'result[y_fit_series] = df.apply(lowess_fit, axis=1, args=(x_series, y_series, fit_size))\n'
    ;
    tbl
     | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>使用情况

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
//
// Apply 9 points LOWESS regression on regular time series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| make-series num=count() on TimeStamp from max_t-1d to max_t step 5m by OsVer
| extend fnum = dynamic(null)
| invoke series_fit_lowess_fl('num', 'fnum', 9)
| render timechart
```

---

:::image type="content" source="images/series-fit-lowess-fl/lowess-regular-time-series.png" alt-text="Graph showing nine points LOWESS 拟合到规则时序" border="false":::

## <a name="examples"></a>示例

以下示例假定已安装该函数：

### <a name="test-irregular-time-series"></a>测试不规则时序

下面的示例测试不规则（间距不均匀）时序

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
let max_t = datetime(2016-09-03);
demo_make_series1
| where TimeStamp between ((max_t-1d)..max_t)
| summarize num=count() by bin(TimeStamp, 5m), OsVer
| order by TimeStamp asc
| where hourofday(TimeStamp) % 6 != 0   //  delete every 6th hour to create irregular time series
| summarize TimeStamp=make_list(TimeStamp), num=make_list(num) by OsVer
| extend fnum = dynamic(null)
| invoke series_fit_lowess_fl('num', 'fnum', 9, 'TimeStamp', True)
| render timechart 
```

:::image type="content" source="images/series-fit-lowess-fl/lowess-irregular-time-series.png" alt-text="Graph showing nine points LOWESS 拟合到不规则时序" border="false":::

将 LOWESS 与多项式拟合进行比较

下面的示例包含 x 和 y 轴上有干扰信息的第五阶多项式。 请参阅 LOWESS 与多项式拟合的比较。 

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
range x from 1 to 200 step 1
| project x = rand()*5 - 2.3
| extend y = pow(x, 5)-8*pow(x, 3)+10*x+6
| extend y = y + (rand() - 0.5)*0.5*y
| summarize x=make_list(x), y=make_list(y)
| extend y_lowess = dynamic(null)
| invoke series_fit_lowess_fl('y', 'y_lowess', 15, 'x')
| extend series_fit_poly(y, x, 5)
| project x, y, y_lowess, y_polynomial=series_fit_poly_y_poly_fit
| render linechart
```

:::image type="content" source="images/series-fit-lowess-fl/lowess-vs-poly-fifth-order-noise.png" alt-text="Graphs of LOWESS 针对 x 和 y 轴上有干扰信息的第五阶多项式的多项式拟合":::
