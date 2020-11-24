---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 10/01/2020
title: 跟踪机器学习训练运行 - Azure Databricks
description: 了解如何使用 MLflow 跟踪机器学习训练运行。
ms.openlocfilehash: e72a2f2a739535309603d8f0cd84c33514678280
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589998"
---
# <a name="track-machine-learning-training-runs"></a><a id="mlflow-tracking"> </a><a id="track-machine-learning-training-runs"> </a>跟踪机器学习训练运行

可以通过 MLflow 跟踪组件记录与训练机器学习模型相关的源属性、参数、指标、标记和项目。 MLflow 跟踪基于两个概念，即试验和运行：

* MLflow 试验是组织的基本构成单位，并且是对 MLflow 运行的访问控制；所有 MLflow 运行都属于一个试验。 可以通过试验来可视化、搜索和比较运行，以及下载运行项目和元数据以便在其他工具中进行分析。
* 一个 MLflow 运行对应于模型代码的单次执行。 每个运行都记录以下信息：
  * **源**：启动了此运行的笔记本的名称，或者此运行的项目名称和入口点。
  * **版本**：如果从笔记本运行，则为笔记本修订版本；如果从 [MLflow 项目](projects.md#mlflow-projects)运行，则为 Git 提交哈希。
  * **开始和结束时间**：运行的开始和结束时间。
  * **参数**：保存为键值对的模型参数。 键和值都是字符串。
  * **指标**：保存为键值对的模型评估指标。 值为数字。 每个指标都可以在整个运行过程中更新（例如，用于跟踪模型的损失函数如何聚合），而 MLflow 会记录指标的历史记录并将其可视化。
  * **标记**：运行保存为键值对的元数据。 可以在运行过程中和运行完成后更新标记。 键和值都是字符串。
  * **项目**：任意格式的输出文件。 例如，可以将图像、模型（例如，pickle 格式的 scikit-learn 模型）和数据文件（例如，Parquet 文件）记录为项目。

使用 [MLflow 跟踪 API](https://www.mlflow.org/docs/latest/tracking.html) 可以记录模型运行中的参数、指标、标记和项目。 跟踪 API 可与 MLflow [跟踪服务器](https://www.mlflow.org/docs/latest/tracking.html#tracking-server)通信。 使用 Databricks 时，Databricks 托管的跟踪服务器会记录数据。 托管的 MLflow 跟踪服务器具有 Python API、Java API 和 R API。

若要了解如何控制对试验的访问，请参阅 [MLflow 试验权限](../../security/access-control/workspace-acl.md#mlflow-experiment-permissions)。

> [!NOTE]
>
> MLflow 安装在 Databricks Runtime ML 群集上。 若要在 Databricks Runtime 群集上使用 MLflow，必须安装 PyPI 库 `mlflow[extras]`。 请参阅[在群集上安装库](../../libraries/cluster-libraries.md#install-libraries)。

## <a name="where-mlflow-runs-are-logged"></a>在何处记录 MLflow 运行

所有 MLflow 运行都会记录到活动试验中，可以使用以下任一方法对其进行设置：

* 使用 [mlflow.set_experiment() 命令](https://mlflow.org/docs/latest/python_api/mlflow.html#mlflow.set_experiment)。
* 使用 [mlflow.start_run() 命令](https://www.mlflow.org/docs/latest/python_api/mlflow.html#mlflow.start_run)中的 `experiment_id` 参数。
* 设置 MLflow 环境变量 [MLFLOW_EXPERIMENT_NAME 或 MLFLOW_EXPERIMENT_ID](https://mlflow.org/docs/latest/cli.html#cmdoption-mlflow-run-arg-uri) 之一。

如果未设置活动试验，则会将运行记录到[笔记本试验](#mlflow-notebook-experiments)。

## <a name="experiments"></a><a id="experiments"> </a><a id="mlflow-experiments"> </a>试验

有两种类型的试验：工作区和笔记本。

* 可以从工作区 UI 或 MLflow API 创建工作区试验。 工作区试验不与任何笔记本关联，任何笔记本都可以使用试验 ID 或试验名称将运行记录到这些试验中。
* 笔记本试验与特定笔记本相关联。 如果在使用 [mlflow.start_run()](https://mlflow.org/docs/latest/python_api/mlflow.html#mlflow.start_run) 启动运行时没有活动的试验，Azure Databricks 会自动创建笔记本试验。

若要了解如何控制对试验的访问，请参阅 [MLflow 试验权限](../../security/access-control/workspace-acl.md#mlflow-experiment-permissions)。

### <a name="workspace-experiments"></a>工作区试验

此部分介绍如何使用 Azure Databricks UI 创建工作区试验。 也可使用 [MLflow API](https://mlflow.org/docs/latest/index.html)。

有关如何将运行记录到工作区试验的说明，请参阅[将运行记录到笔记本或工作区试验](#log-runs-to-a-notebook-or-workspace-experiment)。

#### <a name="create-workspace-experiment"></a>创建工作区试验

1. 单击边栏中的“工作区”按钮 ![“工作区”图标](../../_static/images/workspace/workspace-icon.png) 或“主页”按钮 ![“主页”图标](../../_static/images/workspace/home-icon.png)。
2. 转到要在其中创建试验的文件夹。
3. 执行下列操作之一：
   * 在任何文件夹旁边，单击文本右侧的 ![菜单下拉列表](../../_static/images/menu-dropdown.png)，然后选择“创建”>“MLflow 试验”。

     > [!div class="mx-imgBorder"]
     > ![创建试验](../../_static/images/workspace/mlflow-experiments-create-azure.png)

   * 在工作区或用户文件夹中，单击 ![向下的脱字号](../../_static/images/down-caret.png)，然后选择“创建”>“MLflow 试验”。
4. 在“创建 MLflow 试验”对话框中，输入试验的名称，还可以选择输入项目位置。 如果未指定项目位置，则项目会存储在 `dbfs:/databricks/mlflow-tracking/<experiment-id>` 中。

   Azure Databricks 支持 [DBFS](../../data/databricks-file-system.md) 和 Azure Blob 存储项目位置。

   若要将项目存储在 Azure Blob 存储中，请指定 `wasbs://<container>@<storage-account>.blob.core.windows.net/<path>` 格式的 URI。 无法在 MLflow UI 中查看存储在 Azure Blob 存储中的项目；必须使用 blob 存储客户端下载它们。

5. 单击“创建”。 此时会显示一个空试验。

### <a name="view-workspace-experiment"></a>查看工作区试验

1. 单击边栏中的“工作区”按钮 ![“工作区”图标](../../_static/images/workspace/workspace-icon.png) 或“主页”按钮 ![“主页”图标](../../_static/images/workspace/home-icon.png)。
2. 转到包含该试验的文件夹。
3. 单击试验名称。

### <a name="delete-workspace-experiment"></a>删除工作区试验

1. 单击边栏中的“工作区”按钮 ![“工作区”图标](../../_static/images/workspace/workspace-icon.png) 或“主页”按钮 ![“主页”图标](../../_static/images/workspace/home-icon.png)。
2. 转到包含该试验的文件夹。
3. 单击试验右侧的 ![菜单下拉列表](../../_static/images/menu-dropdown.png)，然后选择“移至回收站”。

### <a name="notebook-experiments"></a><a id="mlflow-notebook-experiments"> </a><a id="notebook-experiments"> </a>笔记本试验

在笔记本中使用 [mlflow.start_run() 命令](https://mlflow.org/docs/latest/python_api/mlflow.html#mlflow.start_run)时，运行会将指标和参数记录到活动试验中。 如果没有活动的试验，Azure Databricks 会创建笔记本试验。 笔记本试验的名称和 ID 与相应笔记本的名称和 ID 相同。 笔记本 ID 是[笔记本 URL 和 ID](../../workspace/workspace-details.md#workspace-notebook-url) 末尾的数字标识符。

有关如何将运行记录到笔记本试验的说明，请参阅[将运行记录到笔记本或工作区试验](#log-runs-to-a-notebook-or-workspace-experiment)。

> [!NOTE]
>
> 如果使用 API（例如，Python 中的 `MlflowClient.tracking.delete_experiment()`）删除笔记本试验，则笔记本本身会被移到回收站文件夹中。

#### <a name="view-notebook-experiment"></a>查看笔记本试验

若要查看笔记本试验及其关联的运行，请单击笔记本工具栏中的“试验”图标 ![试验](../../_static/images/access-control/experiment.png)：

> [!div class="mx-imgBorder"]
> ![笔记本工具栏](../../_static/images/mlflow/notebook-toolbar.png)

在“试验运行”边栏中，你可以查看运行参数和指标：

> [!div class="mx-imgBorder"]
> ![查看运行参数和指标](../../_static/images/mlflow/mlflow-notebook-revision.png)

你还可以查看创建了此运行的笔记本的版本。 单击该试验运行的框中的“笔记本”图标 ![NotebookVersion](../../_static/images/access-control/notebook-version.png)。 与该运行关联的笔记本的版本会在主窗口中显示，窗口中突出显示的条形显示了运行的日期和时间。

单击“试验运行”上下文栏中的 ![外部链接](../../_static/images/external-link.png) 图标以查看试验：

> [!div class="mx-imgBorder"]
> ![查看试验](../../_static/images/mlflow/quick-start-nb-experiment.png)

在“试验运行”边栏中，单击日期旁边的 ![外部链接](../../_static/images/external-link.png) 图标 ![运行日期](../../_static/images/mlflow/run-in-sidebar.png)

以查看某个运行：

> [!div class="mx-imgBorder"]
> ![查看运行](../../_static/images/mlflow/quick-start-nb-run.png)

#### <a name="delete-notebook-experiment"></a>删除笔记本试验

笔记本试验是笔记本的一部分，不能单独删除。 如果删除笔记本，则会删除笔记本试验。 如果使用 API（例如，Python 中的 `MlflowClient.tracking.delete_experiment()`）删除笔记本试验，则也会删除笔记本。

## <a name="runs"></a>运行次数

所有 MLflow 运行都会记录到[活动试验](#where-mlflow-runs-are-logged)中。 如果尚未将某个试验显式设置为活动试验，则会将运行记录到笔记本试验。

### <a name="log-runs-to-a-notebook-or-workspace-experiment"></a><a id="log-runs-to-a-notebook-or-workspace-experiment"> </a><a id="mlflow-recording-runs"> </a>将运行记录到笔记本或工作区试验

此笔记本演示的示例说明了如何将运行记录到笔记本试验和工作区试验。 只有在笔记本中启动的 MLflow 运行才能记录到笔记本试验中。 从任何笔记本启动的或从 API 启动的 MLflow 运行可以记录到工作区试验中。 若要了解如何查看已记录的运行，请参阅[查看笔记本试验](#view-notebook-experiment)和[查看工作区试验](#view-workspace-experiment)。

#### <a name="log-mlflow-runs-notebook"></a>将 MLflow 运行记录到笔记本

[获取笔记本](../../_static/notebooks/mlflow/mlflow-log-runs.html)

可以使用 MLflow Python、Java 或 Scala 以及 R API 来启动运行并记录运行数据。 以下部分提供了详细信息。 如需示例笔记本，请参阅[快速入门](quick-start.md#mlflow-quick-start)。

#### <a name="python"></a>Python

1. 将 PyPI 库 `mlflow[extras]` 安装到群集，其中的额外依赖项包括：
   * `scikit-learn`（当 Python 版本 >= ‘3.5’ 时）
   * `scikit-learn` == 0.20（当 Python 版本 < ‘3.5’ 时）
   * `boto3` >= 1.7.12
   * `mleap` >= 0.8.1
   * `azure-storage`
   * `google-cloud-storage`
2. 导入 MLflow 库：

   ```python
   import mlflow
   ```

3. 启动 MLflow 运行：

   ```python
   with mlflow.start_run() as run:
   ```

4. 记录参数、指标和项目：

   ```python
   # Log a parameter (key-value pair)
   mlflow.log_param("param1", 5)
   # Log a metric; metrics can be updated throughout the run
   mlflow.log_metric("foo", 2, step=1)
   mlflow.log_metric("foo", 4, step=2)
   mlflow.log_metric("foo", 6, step=3)
   # Log an artifact (output file)
   with open("output.txt", "w") as f:
       f.write("Hello world!")
   mlflow.log_artifact("output.txt")
   ```

#### <a name="scala"></a>Scala

1. 将 PyPI 库 `mlflow` 和 Maven 库 `org.mlflow:mlflow-client:1.0.0` 安装到群集。
2. 导入 MLflow 和文件库：

   ```scala
   import org.mlflow.tracking.ActiveRun
   import org.mlflow.tracking.MlflowContext
   import java.io.{File,PrintWriter}
   ```

3. 创建 MLflow 上下文：

   ```scala
   val mlflowContext = new MlflowContext()
   ```

4. 创建试验。

   ```scala
   val experimentName = "/Shared/QuickStart"
   val mlflowContext = new MlflowContext()
   val client = mlflowContext.getClient()
   val experimentOpt = client.getExperimentByName(experimentName);
   if (!experimentOpt.isPresent()) {
    client.createExperiment(experimentName)
   }
   mlflowContext.setExperimentName(experimentName)
   ```

5. 记录参数、指标和文件：

   ```scala
   import java.nio.file.Paths
   val run = mlflowContext.startRun("run")
   // Log a parameter (key-value pair)
   run.logParam("param1", "5")
   // Log a metric; metrics can be updated throughout the run
   run.logMetric("foo", 2.0, 1)
   run.logMetric("foo", 4.0, 2)
   run.logMetric("foo", 6.0, 3)
    new PrintWriter("/tmp/output.txt") { write("Hello, world!") ; close }
    run.logArtifact(Paths.get("/tmp/output.txt"))
   ```

6. 关闭运行：

   ```scala
   run.endRun()
   ```

#### <a name="r"></a>R

1. 将 CRAN 库 `mlflow` 安装到群集。
2. 导入并安装 MLflow 库：

   ```r
   library(mlflow)
   install_mlflow()
   ```

3. 创建新运行：

   ```r
   run <- mlflow_start_run()
   ```

4. 记录参数、指标和文件：

   ```r
   # Log a parameter (key-value pair)
   mlflow_log_param("param1", 5)
   # Log a metric; metrics can be updated throughout the run
   mlflow_log_metric("foo", 2, step = 1)
   mlflow_log_metric("foo", 4, step = 2)
   mlflow_log_metric("foo", 6, step = 3)
   # Log an artifact (output file)
   write("Hello world!", file = "output.txt")
   mlflow_log_artifact("output.txt")
   ```

5. 关闭运行：

   ```r
   mlflow_end_run()
   ```

### <a name="view-and-manage-runs-in-experiments"></a><a id="mlflow-runs"> </a><a id="view-and-manage-runs-in-experiments"> </a>在试验中查看和管理运行

在试验中，可以对其包含的运行执行许多操作。

<!------------------->

<!--Toggle display-->

<!------------------->

<!--Use the toggle |List Grid Toggle| to switch the display of parameters and metrics lists between horizontal and vertical. The vertical display is useful when you have a lot of parameters or metrics.-->

<!--.. figure:: /_static/images/mlflow/mlflow-run-horizontal.png
:scale: 70%

Horizontal-->

<!--.. figure:: /_static/images/mlflow/mlflow-run-vertical.png
:scale: 70%-->

<!--Vertical-->

#### <a name="filter-runs"></a>筛选运行

若要按参数或指标名称筛选运行，请在“筛选器[参数|指标]”字段中键入参数或指标名称，然后按 **Enter**。

若要筛选与某个包含参数和指标值的表达式匹配的运行，请执行以下操作：

1. 在“搜索运行”字段中，指定一个表达式。 例如：`metrics.r2 > 0.3`。

   > [!div class="mx-imgBorder"]
   > ![筛选运行](../../_static/images/mlflow/mlflow-web-ui.png)

2. 单击“搜索”。 

#### <a name="download-runs"></a>下载运行

1. 选择一个或多个运行。
2. 单击“下载 CSV”。 此时会下载包含以下字段的 CSV 文件：`Run ID,Name,Source Type,Source Name,User,Status,<parameter1>,<parameter2>,...,<metric1>,<metric2>,...`。

#### <a name="display-run-details"></a>显示运行详细信息

单击某个运行的日期链接。 此时会显示运行详细信息屏幕。 此屏幕显示了用于运行的参数、运行所生成的指标以及任何标记或注释。 你还可以在此屏幕中访问从运行中保存的项目。

若要查看用于运行的笔记本或 Git 项目的特定版本，请执行以下操作：

* 如果运行已在 Azure Databricks 笔记本或作业中以本地方式启动，请单击“源”字段中的链接以打开在运行中使用的特定[笔记本版本](../../notebooks/notebooks-use.md#version-control)。
* 如果已从 [Git 项目](projects.md#remote-run)以远程方式启动了运行，则请单击“Git 提交”字段中的链接，以打开运行中所用项目的特定版本。 “源”字段中的链接可打开运行中所用的 Git 项目的 master 分支。

#### <a name="compare-runs"></a>比较运行

1. 在试验中，通过单击运行左侧的复选框来选择两个或多个运行。
2. 单击“比较”。 此时会显示“比较 <N> 个运行”屏幕。
3. 执行下列操作之一：
   * 选择指标名称以显示指标图形。
   * 从“X 轴”和“Y 轴”下拉列表中选择参数和指标来生成散点图。

     > [!div class="mx-imgBorder"]
     > ![散点图](../../_static/images/mlflow/mlflow-run-comparison.png)

#### <a name="delete-runs"></a>删除运行

1. 在试验中，通过单击运行左侧的复选框来选择一个或多个运行。
2. 单击 **“删除”** 。
3. 如果运行为父运行，则确定是否也要删除后代运行。 默认情况下选择此选项。
4. 单击“删除”进行确认，或单击“取消”进行取消。 删除的运行保存 30 天。 若要显示删除的运行，请选择“状态”字段中的“已删除”。

## <a name="access-the-mlflow-tracking-server-from-outside-azure-databricks"></a>从 Azure Databricks 外部访问 MLflow 跟踪服务器

你还可以从 Azure Databricks 外部写入和读取跟踪服务器，例如，使用 MLflow CLI 来这样做。

* [从 Azure Databricks 外部访问 MLflow 跟踪服务器](access-hosted-tracking-server.md)

## <a name="analyze-mlflow-runs-using-dataframes"></a><a id="analyze-mlflow-runs-using-dataframes"> </a><a id="analyzing-runs"> </a>使用数据帧分析 MLflow 运行

可使用以下两个数据帧 API 以编程方式访问 MLflow 运行数据：

* MLflow Python 客户端 [search_runs API](https://mlflow.org/docs/latest/python_api/mlflow.html#mlflow.search_runs) 返回 pandas 数据帧。
* [MLflow 试验](../../data/data-sources/mlflow-experiment.md#mlflow-exp-datasource)数据源返回 Apache Spark 数据帧。

此示例演示如何使用 MLflow Python 客户端生成一个仪表板。该仪表板可直观显示一段时间内的评估指标更改、跟踪特定用户启动的运行的数目，以及度量所有用户的运行总数：

* [使用 MLflow 搜索 API 构建仪表板](build-dashboards.md)

## <a name="examples"></a><a id="examples"> </a><a id="tracking-examples"> </a>示例

以下笔记本演示了如何在 MLflow 中训练多种类型的模型和跟踪训练数据，以及如何在 Delta Lake 中存储跟踪数据。

* [训练 scikit-learn 模型并以 scikit-learn 格式保存](tracking-ex-scikit.md)
* [训练 PyTorch 模型](tracking-ex-pytorch.md)
* [训练 PySpark 模型并以 MLeap 格式保存](tracking-ex-pyspark.md)
* [使用 Delta Lake 跟踪 ML 模型训练数据](tracking-ex-delta.md)