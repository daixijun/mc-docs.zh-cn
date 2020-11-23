---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 分布式 Hyperopt 和自动化 MLflow 跟踪 - Azure Databricks
description: 了解如何在使用 Hyperopt 优化机器学习模型时使用自动 MLflow 跟踪。
ms.openlocfilehash: 855f7446584c356ac700c1929b33ae50c54f557d
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589681"
---
# <a name="distributed-hyperopt-and-automated-mlflow-tracking"></a>分布式 Hyperopt 和自动化 MLflow 跟踪

[用于机器学习的 Databricks Runtime](../../../runtime/mlruntime.md) 包括 Hyperopt，并通过 Apache Spark 支持的实现进行了增强。  通过使用 `hyperopt.Trials` 的 `SparkTrials` 扩展，你可以轻松地分发 Hyperopt 运行，而无需对 Hyperopt 的用法进行其他更改。 应用 `hyperopt.fmin()` 函数时，会传入 `SparkTrials` 类。 `SparkTrials` 通过向 Spark 辅助角色分配“试验”，可以加速单机优化。

[MLflow](../../mlflow/index.md#mlflow-guide) 是用于管理端到端机器学习生命周期的开源平台。 对 MLflow 的支持级别取决于 Databricks Runtime 版本：

* [Databricks Runtime 5.5 LTS ML](../../../release-notes/runtime/5.5ml.md)
  * 支持自动化 [MLflow 跟踪](../../mlflow/tracking.md#mlflow-tracking)，用于在 Python 中使用 Hyperopt 和 `SparkTrials` 实现超参数优化。 启用自动化 MLflow 跟踪并使用 `SparkTrials` 运行 `fmin()` 时，系统会自动在 MLflow 中记录超参数和评估指标。 如果没有自动化 MLflow 跟踪，需要进行显式 API 调用来记录 MLflow。 自动化 MLflow 跟踪功能是默认启用的。 若要禁用此设置，请将 [Spark 配置](../../../clusters/configure.md#spark-config) `spark.databricks.mlflow.trackHyperopt.enabled`设置为 `false`。 即使没有自动化 MLflow 跟踪，仍然可以使用 `SparkTrials` 来分发优化。
  * 包括 MLflow，因此无需单独安装。
* [Databricks Runtime 6.3 ML（不受支持）](../../../release-notes/runtime/6.3ml.md)及以上版本支持从辅助角色记录到 MLflow。 可以在传递给 Hyperopt 的目标函数中添加自定义日志代码。

## <a name="how-to-use-hyperopt-with-sparktrials"></a>如何将 Hyperopt 用于 `SparkTrials`

本节介绍如何配置传递给 Hyperopt 的参数、有关使用 Hyperopt 的最佳做法以及使用 Hyperopt 时可能出现的问题的故障排除。

> [!NOTE]
>
> 对于使用 MLlib 创建的模型，不要使用 `SparkTrials`，因为模型构建过程会在群集上自动并行化。  相反，请使用 Hyperopt 类 `Trials`。

### <a name="fmin-arguments"></a>`fmin()` 参数

`fmin()`[文档](https://github.com/hyperopt/hyperopt/wiki/FMin)对所有参数都有详细说明。 下面是重要的内容：

* `fn`：通过从超参数空间 (`space`) 生成的值调用的目标函数。 `fn` 可以以标量值或字典形式返回损失（有关详细信息，请参阅 [Hyperopt 文档](https://github.com/hyperopt/hyperopt)）。 这通常是大多数代码所在的位置，例如，损失计算、模型训练等等。
* `space`：用于生成超参数空间 Hyperopt 搜索的表达式。 一个简单的例子是 `hp.uniform('x', -10, 10)`，它定义了 -10 到 10 之间的一维搜索空间。 Hyperopt 在定义超参数空间方面提供了很大的灵活性。 熟悉 Hyperopt 后，可以使用此参数使优化更有效。
* `algo`：搜索算法 Hyperopt 用于搜索超参数空间 (`space`)。 典型值为随机搜索的 `hyperopt.rand.suggest` 和 TPE 的 `hyperopt.tpe.suggest`。
* `max_evals`：要尝试的超参数设置的数量，也就是要匹配的模型的数量。  此数字应该足够大以摊销开销。
* `max_queue_len`：Hyperopt 应提前生成的超参数设置数目。  由于 Hyperopt TPE 生成算法可能需要一些时间，因此将时间增加到超过默认值 1 的值可能会有所帮助，但通常不会超过 `SparkTrials` 设置 `parallelism`。

### <a name="sparktrials-arguments-and-implementation"></a>`SparkTrials` 参数和实现

本部分描述如何配置传递给 `SparkTrials` 的参数和 `SparkTrials` 的实现方面。

#### <a name="arguments"></a>自变量

* `parallelism`：要同时评估的试验的最大数量。  更高的并行度允许对更多的超参数设置进行横向扩展测试。
  * 权衡：由于 Hyperopt 会基于过去的结果提议新试验，所以在并行度和适应度之间存在一个权衡。  对于固定的 `max_evals`，即 `fmin()` 评估的最大试验数，较高的并行度将导致加速，而较低的并行度可能会得到更好的结果，因为每次迭代都可以访问更多过去的结果。
  * 限制：目前，并行度存在 128 的硬上限。  `SparkTrials` 还将检查群集的配置，以了解 Spark 允许多少并发任务；如果并行度超过这个最大值，`SparkTrials` 将把并行度降低到该最大值。
* `timeout`：`fmin()` 调用所能采用的最大秒数。 超过此数目后，所有运行都将终止，`fmin()` 将退出。 将根据选择的最佳模型保留已完成的运行的所有信息。 此参数可以节省时间，也可以帮助控制群集成本。

[示例笔记本](#example-nb-mlflow)中包含了完整的 `SparkTrials` API。 若要查找它，请搜索 `help(SparkTrials)`。

#### <a name="implementation"></a>实现

当定义传递给 `fmin()` 的目标函数 `fn` 时，以及在选择群集设置时，了解 `SparkTrials` 如何分配优化任务是很有帮助的。

在 Hyperopt 中，一次试验通常相当于在一组超参数上拟合一个模型。 Hyperopt 以迭代方式生成试用，评估它们，并重复执行。

使用 `SparkTrials`，群集的驱动程序节点生成新的试用，工作器节点评估这些试用。  每个试用都是由具有一个任务的 Spark 作业生成的，并在辅助角色计算机上的任务中进行评估。 如果群集设置为每个辅助角色运行多个任务，则可以在该辅助角色上同时评估多个试用。

### <a name="manage-mlflow-runs-with-sparktrials"></a>使用 `SparkTrials` 管理 MLflow 运行

`SparkTrials` 将优化结果记录为嵌套的 MLflow 运行，如下所示：

* 主运行或父运行：对 `fmin()` 的调用被记录为“主”运行。  如果有一个活动的运行，`SparkTrials` 将在这个活动运行下记录，并且在 `fmin()` 返回时不结束运行。  如果没有活动的运行，`SparkTrials` 将创建一个新的运行，在其下进行记录，并在 `fmin()` 返回之前结束该运行。
* 子运行：经过测试的每个超参数设置（“试验”）都记录为主运行下的子运行。  对于运行在 Databricks Runtime 6.0 ML 及更高版本上的群集，来自辅助角色的 MLflow 日志记录也存储在相应的子运行下。

当调用 `fmin()` 时，建议使用活动 MLflow 运行管理；也就是说，将对 `fmin()` 的调用包装在 `with mlflow.start_run():` 语句中。
这样可以确保每个 `fmin()` 调用都记录在其自己的 MLflow“主”运行下，并且可以更轻松地将额外的标记、参数或指标记录到该运行中。

> [!NOTE]
>
> 当在同一个活动 MLflow 运行中多次调用 `fmin()` 时，它会将这些多个 `fmin()` 调用记录到同一个“主”运行中。  若要解决 MLflow 参数和标记的名称冲突，可以通过附加一个 UUID 来处理有冲突的名称。

当从 [Databricks Runtime 6.3 ML（不受支持）](../../../release-notes/runtime/6.3ml.md)及以上版本的辅助角色中进行日志记录时，无需在目标函数中显式管理运行。 在目标函数中调用 `mlflow.log_param("param_from_worker", x)` 以将 param 记录到子运行中。 可以在目标函数中记录参数、指标、标记和项目。

笔记本显示了分布式 Hyperopt + 自动化 MLflow 跟踪的运行情况。

#### <a name="distributed-hyperopt--automated-mlflow-tracking-notebook"></a><a id="distributed-hyperopt--automated-mlflow-tracking-notebook"> </a><a id="example-nb-mlflow"> </a>分布式 Hyperopt + 自动化 MLflow 跟踪笔记本

[获取笔记本](../../../_static/notebooks/hyperopt-spark-mlflow.html)

在笔记本的最后一个单元格中执行操作后，MLflow UI 应显示：

> [!div class="mx-imgBorder"]
> ![Hyperopt MLflow 演示](../../../_static/images/mlflow/hyperopt-spark-mlflow.png)

请参阅[使用分布式 Hyperopt 和自动化 MLflow 跟踪功能为搜索建模](hyperopt-model-selection.md)，了解如何为多个模型优化超参数并获得整体上而言的最佳模型。