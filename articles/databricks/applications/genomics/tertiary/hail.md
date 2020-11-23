---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/04/2020
title: Hail 0.2 - Azure Databricks
description: 了解如何使用用于基因组学的 Databricks Runtime 中包含的 Hail 库来分析大型基因组学数据集。
ms.openlocfilehash: f57f4622773ad4f1458a892300c3a53ad65533c8
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589686"
---
# <a name="hail-02"></a>Hail 0.2

> [!NOTE]
>
> Apache Spark 3.0 尚不支持 Hail，因此在用于基因组学的 Databricks Runtime 7.x 中不可用。 用于基因组学的 Databricks Runtime 6.x 的所有版本均支持 Hail。

[Hail](https://hail.is/) 是基于 Apache Spark 构建的库，用于分析大型基因组学数据集。 Hail 0.2 集成到[用于基因组学的 Databricks Runtime](../../../runtime/genomicsruntime.md#dbr-genomics)。

## <a name="create-a-hail-cluster"></a>创建 Hail 群集

若要创建安装了 Hail 的群集，请执行以下操作：

1. 设置以下[环境变量](../../../clusters/configure.md#environment-variables)：

   ```ini
   ENABLE_HAIL=true
   ```

   此环境变量会导致群集在安装 Hail 0.2 及其依赖项和 Python 3.6 后启动。

## <a name="use-hail-in-a-notebook"></a>在笔记本中使用 Hail

Azure Databricks 中的 Hail 0.2 代码与 Hail 文档的工作原理大致相同。 但 Azure Databricks 环境需要进行一些修改。

### <a name="initialization"></a>初始化

初始化 Hail 时，传入预先创建的 `SparkContext` 并将初始化标记为幂等。 此设置使多个 Azure Databricks 笔记本可以使用相同的 Hail 上下文。

> [!NOTE]
>
> 启用 `skip_logging_configuration`，将日志保存到滚动驱动程序 log4j 输出。 此设置仅在用于基因组学的 Databricks Runtime 6.6 中受支持。

```python
import hail as hl
hl.init(sc, idempotent=True, quiet=True, skip_logging_configuration=True)
```

### <a name="plotting"></a>正在绘图

Hail 使用 [Bokeh](https://docs.bokeh.org/en/latest/) 库创建绘图。 内置到 Bokeh 中的 `show` 函数在 Azure Databricks 中不起作用。 若要显示由 Hail 生成的 Bokeh 图，可以运行以下命令：

```python
from bokeh.embed import components, file_html
from bokeh.resources import CDN
plot = hl.plot.histogram(mt.DP, range=(0,30), bins=30, title='DP Histogram', legend='DP')
html = file_html(plot, CDN, "Chart")
displayHTML(html)
```

有关详细信息，请参阅 [Bokeh](../../../notebooks/visualizations/bokeh.md)。

## <a name="limitations"></a>限制

* 启用 Hail 支持后，群集将使用 Python 3.6，因此针对其他版本的 Python 编写的笔记本可能无法工作。
* 启用 Hail 支持后，默认安装较少的 Python 库。 仍可使用[库](../../../libraries/index.md)功能来安装新的库。

设置 Hail 群集后，请尝试使用 Hail 概述笔记本。

### <a name="hail-overview-notebook"></a>Hail 概述笔记本

[获取笔记本](../../../_static/notebooks/genomics/hail-overview.html)