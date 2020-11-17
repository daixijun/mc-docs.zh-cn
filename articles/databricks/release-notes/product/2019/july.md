---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 2019 年 7 月 - Azure Databricks
description: 新 Azure Databricks 功能和改进的 2019 年 7 月发行说明。
ms.openlocfilehash: a217b428e96e2593b36065a789d71397d56b09bc
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329227"
---
# <a name="july-2019"></a>2019 年 7 月

这些功能和 Azure Databricks 平台的改进已于 2019 年 7 月发布。

> [!NOTE]
>
> 发布分阶段进行。 在初始发布日期后，可能最长需要等待一周，你的 Azure Databricks 帐户才会更新。

## <a name="coming-soon-databricks-60-will-not-support-python-2"></a>即将发生的更改：Databricks 6.0 将不支持 Python 2

预计在即将到来的 Python 2 生命周期结束之后（在 2020 年宣布），Databricks Runtime 6.0 将不支持 Python 2。 更早版本的 Databricks Runtime 将继续支持 Python 2。 我们预计在 2019 年晚些时候发布 Databricks Runtime 6.0。

## <a name="preload-the-databricks-runtime-version-on-pool-idle-instances"></a>在池空闲实例上预先加载 Databricks Runtime 版本

2019 年 7 月 30 日 - 8 月 6 日：版本 2.103

现在可以通过选择要在池中空闲实例上加载的 Databricks Runtime 版本来加快池支持的群集启动。  池 UI 上的字段称为[预加载的 Spark 版本](../../../clusters/instance-pools/configure.md#preloaded-spark)。

> [!div class="mx-imgBorder"]
> ![预加载的 Spark 版本](../../../_static/images/instance-pools/preloaded-spark.png)

## <a name="custom-cluster-tags-and-pool-tags-play-better-together"></a>自定义群集标记和池标记可以更好地配合工作

2019 年 7 月 30 日 - 8 月 6 日：版本 2.103

本月初，Azure Databricks 引入了池，这是一组空闲实例，可帮助你快速启动群集。 在原始版本中，池支持的群集继承了池配置中的默认标记和自定义标记，因此无法在群集级别修改这些标记。 现在，你可以配置特定于池支持的群集的自定义标记，并且该群集将应用所有自定义标记，无论这些标记是从池中继承的还是专门分配到该群集的。 添加特定于群集的自定义标记时，其键名不能与从池继承的自定义标记的键名相同（也就是说，不能重写从池继承的自定义标记）。 有关详细信息，请参阅[池标记](../../../clusters/instance-pools/configure.md#instance-pool-tags)。

## <a name="mlflow-11-brings-several-ui-and-api-improvements"></a>MLflow 1.1 带来了多项 UI 和 API 改进

2019 年 7 月 30 日 - 8 月 6 日：版本 2.103

MLflow 1.1 引入了多项新功能来提高 UI 和 API 可用性：

* 现在，如果运行次数超过 100，可以在“运行概述 UI”中浏览多页的运行信息。 在第 100 次运行之后，单击“加载更多”按钮即可加载随后的 100 次运行。

  > [!div class="mx-imgBorder"]
  > ![分页运行](../../../_static/images/mlflow/paged-runs.gif)

* 比较运行 UI 现在提供一个平行坐标图。 通过此绘图，你可以观察 n 维参数集与指标之间的关系。 它直观显示了所有运行，并以基于指标值（如准确度）的带颜色编码的线表示，还显示了每次运行所采用的参数值。

  > [!div class="mx-imgBorder"]
  > ![平行坐标绘图](../../../_static/images/mlflow/parallel-coordinates.gif)

* 现在可从运行概述 UI 中添加和编辑标记，并在试验搜索视图中查看标记。
* 新的 [MLflowContext API](https://www.mlflow.org/docs/latest/java_api/index.html) 使你能够使用与 Python API 类似的方式创建和记录运行。 此 API 不同于现有的低级别 `MlflowClient` API，后者仅包装 REST API。
* 现在可以使用 [DeleteTag API](https://mlflow.org/docs/latest/python_api/mlflow.tracking.html?highlight=delete_tag#mlflow.tracking.MlflowClient.delete_tag) 从 MLflow 运行中删除标记。

有关详细信息，请参阅 [MLflow 1.1 博客文章](https://databricks.com/blog/2019/07/23/announcing-the-mlflow-1-1-release.html)。 有关功能和修补程序的完整列表，请参阅 [MLflow 1.1 Changelog](https://github.com/mlflow/mlflow/blob/master/CHANGELOG.rst#11-2019-07-22)。

## <a name="pandas-dataframe-display-renders-like-it-does-in-jupyter"></a>pandas 数据帧像在 Jupyter 中一样显示呈现器

2019 年 7 月 30 日 - 8 月 6 日：版本 2.103

现在，当你调用 pandas 数据帧时，它的呈现方式将与在 Jupyter 中的呈现方式相同。

> [!div class="mx-imgBorder"]
> ![显示 pandas 数据帧](../../../_static/images/notebooks/pandas-dataframe-display.gif)

## <a name="new-regions"></a>新区域

**2019 年 7 月 30 日**

目前以下其他区域提供 Azure Databricks：

* 韩国中部
* 南非北部

## <a name="databricks-runtime-55-with-conda-beta"></a>带有 Conda 的 Databricks Runtime 5.5 (Beta)

**2019 年 7 月 23 日**

> [!IMPORTANT]
>
> 带有 Conda 的 Databricks Runtime 以 [Beta 版本](../../release-types.md#runtime-releases)提供。 在即将发布的 Beta 版本中，支持的环境的内容可能会发生变化。 更改可能包括包列表或已安装包的版本的列表。 带有 Conda 的 Databricks Runtime 5.5 是基于 [Databricks Runtime 5.5 LTS](../../runtime/5.5.md) 构建的。

带有 Conda 的 Databricks Runtime 5.5 版本添加了一个新的笔记本范围的库 API，用于支持使用 YAML 规范来更新笔记本的 Conda 环境（请参阅 [Conda 文档](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)）。

请参阅[带有 Conda 的 Databricks Runtime 5.5（Beta 版本）](../../runtime/5.5conda.md)的完整发行说明。

## <a name="updated-metastore-connection-limit"></a>更新了元存储连接限制

2019 年 7 月 16 日至 23 日：版本 2.102

eastus、eastus2、centralus、westus、westus2、westeurope、northeurope 中的新 Azure Databricks 工作区将具有更高的元存储连接上限 (250)。 现有工作区将继续使用当前的元存储，不会受影响和中断，连接限制依然为 100。

## <a name="set-permissions-on-pools-public-preview"></a>设置对池的权限（公共预览版）

2019 年 7 月 16 日至 23 日：版本 2.102

池 UI 现在支持对可管理池的人员以及可将群集附加到池的人员设置权限。

有关详细信息，请参阅[池访问控制](../../../security/access-control/pool-acl.md)。

## <a name="databricks-runtime-55-for-machine-learning"></a>用于机器学习的 Databricks Runtime 5.5

**2019 年 7 月 15 日**

Databricks Runtime 5.5 ML 是基于 [Databricks Runtime 5.5 LTS](../../runtime/5.5.md) 构建的。 它包含许多常见的机器学习库，包括 TensorFlow、PyTorch、Keras 和 XGBoost，并使用 Horovod 提供分布式 TensorFlow 训练。

此版本包含以下新功能和改进：

* 添加了 [MLflow](../../../applications/mlflow/index.md#mlflow-guide) 1.0 Python 包
* 升级了机器学习库
  * Tensorflow 已从 1.12.0 升级到 1.13.1
  * PyTorch 已从 0.4.1 升级到 1.1.0
  * scikit-learn 已从 0.19.1 升级到 0.20.3
* [HorovodRunner](../../../applications/machine-learning/train-model/distributed-training/horovod-runner.md#horovodrunner) 的单节点操作

有关详细信息，请参阅 [Databricks Runtime 5.5 LTS ML](../../runtime/5.5ml.md)。

## <a name="databricks-runtime-55"></a>Databricks Runtime 5.5

**2019 年 7 月 15 日**

Databricks Runtime 5.5 现已推出。  Databricks Runtime 5.5 包括 Apache Spark 2.4.3、已升级的 Python、R、Java 和 Scala 库，以及以下新增功能：

* Azure Databricks 上的 Delta Lake 自动优化 GA
* Azure Databricks 上的 Delta Lake 提升了最小值、最大值和计数聚合查询性能
* 处理速度更快的模型推理管道以及经过优化的二进制文件数据源和标量迭代器 pandas UDF（公共预览版）
* R 笔记本中的机密 API

有关详细信息，请参阅 [Databricks Runtime 5.5 LTS](../../runtime/5.5.md)。

## <a name="keep-a-pool-of-instances-on-standby-for-quick-cluster-launch-public-preview"></a>将实例池保持后备状态以快速启动群集（公共预览版）

2019 年 7 月 9 日至 11 日：版本 2.101

为了缩短群集启动时间，Azure Databricks 现在支持将群集附加到空闲实例的预定义池。 附加到池时，群集会从池中分配其驱动程序节点和工作器节点。 如果池中没有足够的空闲资源来满足群集的请求，则池会通过从云提供程序分配新的实例进行扩展。 终止附加的群集后，它使用的实例会返回到池中，可供其他群集重复使用。

当实例在池中处于空闲状态时，Azure Databricks 不会收取 DBU 费用， 但这会产生实例提供程序费用，具体请参阅[定价](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。

有关详细信息，请参阅[池](../../../clusters/instance-pools/index.md)。

## <a name="ganglia-metrics"></a>Ganglia 指标

2019 年 7 月 9 日至 11 日：版本 2.101

[Ganglia](http://ganglia.sourceforge.net/) 是一种可缩放的分布式监视系统，当前可在 Azure Databricks 群集中使用。 Ganglia 指标有助于监视群集性能和运行状况。 可以从群集详细信息页访问 Ganglia 指标：

> [!div class="mx-imgBorder"]
> ![“Ganglia 指标”选项卡](../../../_static/images/clusters/metrics-tab.png)

有关使用和配置 Ganglia 指标的详细信息，请参阅 [Ganglia 指标](../../../clusters/clusters-manage.md#ganglia-metrics)。

## <a name="global-series-color"></a>全局系列颜色

2019 年 7 月 9 日至 11 日：版本 2.101

现在可以指定某个系列的颜色在笔记本中的所有图表中应保持一致。 请参阅[图表之间的颜色一致性](../../../notebooks/visualizations/index.md#color-consistency-across-charts)。

> [!div class="mx-imgBorder"]
> ![全局系列颜色](../../../_static/images/notebooks/series-colors.gif)