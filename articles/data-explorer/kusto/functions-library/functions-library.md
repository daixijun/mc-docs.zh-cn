---
title: 函数库 - Azure 数据资源管理器
description: 本文介绍用于扩展 Azure 数据资源管理器功能的用户定义函数。
author: orspod
ms.author: v-tawe
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
origin.date: 09/08/2020
ms.date: 01/22/2021
ms.openlocfilehash: 9da8c2adcbc707979cb95a6c12fefb39ad6007a1
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611481"
---
# <a name="functions-library"></a>函数库

下文包含 [UDF（用户定义的函数）](../query/functions/user-defined-functions.md)的分类列表。

文章中提供了用户定义的函数代码。  此代码可在查询中嵌入的 let 语句内使用，也可保留在使用 [`.create function`](../management/create-function.md) 的数据库中。

## <a name="machine-learning-functions"></a>机器学习函数

|函数名称     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[kmeans_fl()](kmeans-fl.md)|使用 k-means 算法进行聚类。 |
|[predict_fl()](predict-fl.md)|使用经过训练的现有机器学习模型进行预测。 |
|[predict_onnx_fl()](predict-onnx-fl.md)| 使用 ONNX 格式的经过训练的现有机器学习模型进行预测。 |

## <a name="promql-functions"></a>PromQL 函数

以下部分包含常用的 [PromQL](https://prometheus.io/docs/prometheus/latest/querying/basics/) 函数。 这些函数可用于分析由 [Prometheus](https://prometheus.io/) 监视系统引入到 Azure 数据资源管理器的指标。 所有函数均假定 Azure 数据资源管理器中的指标使用 [Prometheus 数据模型](https://prometheus.io/docs/concepts/data_model/)进行结构化。


|函数名称     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[series_metric_fl()](series-metric-fl.md)|选择并检索利用 Prometheus 数据模型存储的时序。 |
|[series_rate_fl()](series-rate-fl.md)|计算计数器指标每秒提高的平均速率。 |

## <a name="series-processing-functions"></a>序列处理函数

|函数名称     |描述                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_fl()](quantize-fl.md)|量化指标列。 |
|[series_dot_product_fl()](series-dot-product-fl.md)|计算两个数值向量的点积。 |
|[series_downsample_fl()](series-downsample-fl.md)|按整数因子对时序减少采样。 |
|[series_exp_smoothing_fl()](series-exp-smoothing-fl.md)|对序列应用基本指数平滑筛选器。 |
|[series_fit_lowess_fl()](series-fit-lowess-fl.md)|使用 LOWESS 方法对序列进行局部多项式拟合。 |
|[series_fit_poly_fl()](series-fit-poly-fl.md)|使用回归分析将多项式拟合到序列。 |
|[series_moving_avg_fl()](series-moving-avg-fl.md)|对序列应用移动平均滤波器。 |
|[series_rolling_fl()](series-rolling-fl.md)|对序列应用滚动聚合函数。 |
