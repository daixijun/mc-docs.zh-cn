---
title: series_exp_smoothing_fl() - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的用户定义函数 series_exp_smoothing_fl()。
author: orspod
ms.author: v-tawe
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
origin.date: 11/22/2020
ms.date: 01/22/2021
ms.openlocfilehash: 258461547979c4644c64baf05da44acae80d515f
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614869"
---
# <a name="series_exp_smoothing_fl"></a>series_exp_smoothing_fl()

对序列应用基本指数平滑滤波器。

函数 `series_exp_smoothing_fl()` 接受一个包含动态数值数组的表达式作为输入，并应用[基本指数平滑](https://en.wikipedia.org/wiki/Exponential_smoothing#Basic_(simple)_exponential_smoothing_(Holt_linear))滤波器。

> [!NOTE]
> 此函数是 [UDF（用户定义的函数）](../query/functions/user-defined-functions.md)。 有关详细信息，请参阅[用法](#usage)。

## <a name="syntax"></a>语法

`series_exp_smoothing_fl(`*y_series*`, [`*alpha*`])`
  
## <a name="arguments"></a>参数

* y_series：数值的动态数组单元。
* alpha：范围 [0-1] 中的可选实数值，指定最后一个点的权重与前一个点的权重（即 `1-alpha`）。 默认值为 0.5。

## <a name="usage"></a>使用情况

`series_exp_smoothing_fl()` 是用户定义的函数。 可以在查询中嵌入其代码，或将其安装在数据库中。 用法选项有两种：临时使用和永久使用。 有关示例，请参阅下面的选项卡。

# <a name="ad-hoc"></a>[临时](#tab/adhoc)

如果是临时使用，请使用 [let 语句](../query/letstatement.md)嵌入其代码。 不需要权限。

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
let series_exp_smoothing_fl = (y_series:dynamic, alpha:double=0.5)
{
    series_iir(y_series, pack_array(alpha), pack_array(1, alpha-1))
}
;
range x from 1 to 50 step 1
| extend y = x % 10
| summarize x = make_list(x), y = make_list(y)
| extend exp_smooth_y = series_exp_smoothing_fl(y, 0.4) 
| render linechart
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

如果是永久使用，请使用 [`.create function`](../management/create-function.md)。 创建函数需要有[数据库用户权限](../management/access-control/role-based-authorization.md)。

### <a name="one-time-installation"></a>一次性安装

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Basic exponential smoothing for a series")
series_exp_smoothing_fl(y_series:dynamic, alpha:double=0.5)
{
    series_iir(y_series, pack_array(alpha), pack_array(1, alpha-1))
}
```

### <a name="usage"></a>使用情况

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
range x from 1 to 50 step 1
| extend y = x % 10
| summarize x = make_list(x), y = make_list(y)
| extend exp_smooth_y = series_exp_smoothing_fl(y, 0.4) 
| render linechart
```

---

:::image type="content" source="images/series-exp-smoothing-fl/exp-smoothing-demo.png" alt-text="显示人工序列指数平滑的图" border="false":::
