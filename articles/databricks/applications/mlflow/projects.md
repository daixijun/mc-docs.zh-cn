---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/25/2020
title: 在 Azure Databricks 上运行 MLflow 项目 - Azure Databricks
description: 了解 MLflow 项目以及如何在 Azure Databricks 上远程运行 MLflow 项目。
ms.openlocfilehash: a591db5767f15474c6aeeac6bda7e25c78d6ab90
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589663"
---
# <a name="run-mlflow-projects-on-azure-databricks"></a><a id="mlflow-projects"> </a><a id="run-mlflow-projects-on-azure-databricks"> </a>在 Azure Databricks 上运行 MLflow 项目

[MLflow 项目](https://mlflow.org/docs/latest/projects.html#)是一种用于以可重用和可重现的方式打包数据科学代码的格式。 MLflow 项目组件包括用于运行项目的 API 和命令行工具，这些工具还与跟踪组件集成，以自动记录源代码的参数和 Git 提交，实现可重现性。

本文介绍了 MLflow 项目的格式，以及如何使用 MLflow CLI 在 Azure Databricks 群集上远程运行 MLflow 项目，以轻松纵向缩放数据科学代码。

Databricks Community Edition 不支持 MLflow 项目执行。

## <a name="mlflow-project-format"></a>MLflow 项目格式

任何本地目录或 Git 存储库都可以视为 MLflow 项目。 项目使用以下约定进行定义：

* 项目的名称是目录的名称。
* 在 `conda.yaml`中（如果存在）指定 Conda 环境。 如果不存在 `conda.yaml` 文件，MLflow 在运行项目时使用仅包含 Python（特别是可用于 Conda 的最新 Python）的 Conda 环境。
* 项目中的任何 `.py` 或 `.sh` 文件都可以是入口点，不显式声明参数。 当使用一组参数运行此类命令时，MLflow 会使用 `--key <value>` 语法在命令行上传递每个参数。

可通过添加 MLproject 文件来指定更多选项，MLproject 文件是 YAML 语法中的文本文件。 示例 MLproject 文件如下所示：

```yaml
name: My Project

conda_env: my_env.yaml

entry_points:
  main:
    parameters:
      data_file: path
      regularization: {type: float, default: 0.1}
    command: "python train.py -r {regularization} {data_file}"
  validate:
    parameters:
      data_file: path
    command: "python validate.py {data_file}"
```

## <a name="run-an-mlflow-project"></a>运行 MLflow 项目

若要在默认工作区中的 Azure Databricks 群集上运行 MLflow 项目，请使用以下命令：

```bash
mlflow run <uri> -b databricks --backend-config <json-new-cluster-spec>
```

其中，`<uri>` 是包含 MLflow 项目的 Git 存储库 URI 或文件夹，`<json-new-cluster-spec>` 是包含[群集规范](../../dev-tools/api/latest/jobs.md#jobsclusterspecnewcluster)的 JSON 文档。 Git URI 的格式应为：`https://github.com/<repo>#<project-folder>`。

群集规范示例如下：

```json
{
  "spark_version": "5.2.x-scala2.11",
  "num_workers": 1,
  "node_type_id": "Standard_DS3_v2"
}
```

如果需要在辅助角色上安装库，请使用“群集规范”格式。 注意，wheel 必须上传到 DBFS 并指定为 `pypi` 依赖项。 例如：

```json
{
  "new_cluster": {
    "spark_version": "5.2.x-scala2.11",
    "num_workers": 1,
    "node_type_id": "Standard_DS3_v2"
  },
  "libraries": [
    {
      "pypi": {
        "package": "tensorflow"
      }
    },
    {
      "pypi": {
         "package": "/dbfs/path_to_my_lib.whl"
      }
    }
  ]
}
```

> [!IMPORTANT]
>
> * MLflow 项目不支持 `.egg` 和 `.jar` 依赖项。
> * 不支持使用 Docker 环境执行 MLflow 项目。
> * 在 Databricks 上运行 MLflow 项目时，必须使用新的群集规范。 不支持对现有群集运行项目。

### <a name="using-sparkr"></a>使用 SparkR

若要在 MLflow 项目运行中使用 SparkR，项目代码必须首先安装并导入 SparkR，如下所示：

```r
if (file.exists("/databricks/spark/R/pkg")) {
    install.packages("/databricks/spark/R/pkg", repos = NULL)
} else {
    install.packages("SparkR")
}

library(SparkR)
```

这样项目才可以初始化 SparkR 会话并正常使用 SparkR：

```r
sparkR.session()
...
```

## <a name="example"></a><a id="example"> </a><a id="remote-run"> </a>示例

此示例展示了如何创建试验，在 Azure Databricks 群集上运行 MLflow 教程项目，查看作业运行输出以及查看试验中的运行情况。

### <a name="prerequisites"></a>先决条件

1. 使用 `pip install mlflow` 安装 MLflow。
2. 安装并配置 [Databricks CLI](../../dev-tools/cli/index.md)。 在 Azure Databricks 群集上运行作业需要 Databricks CLI 身份验证机制。

### <a name="step-1-create-an-experiment"></a>步骤 1：创建试验

1. 在工作区中，选择“创建”>“MLflow 试验”。
2. 在“名称”字段中，输入 `Tutorial`。
3. 单击“创建”。 记下试验 ID。 在此示例中，它是 `14622565`。

   > [!div class="mx-imgBorder"]
   > ![试验 ID](../../_static/images/mlflow/mlflow-experiment-id.png)

### <a name="step-2-run-the-mlflow-tutorial-project"></a>步骤 2：运行 MLflow 教程项目

以下步骤设置 `MLFLOW_TRACKING_URI` 环境变量并运行项目，将训练参数、指标和训练模型记录到前一步中提到的试验中：

1. 将 `MLFLOW_TRACKING_URI` 环境变量设置为 Azure Databricks 工作区。

   ```bash
   export MLFLOW_TRACKING_URI=databricks
   ```

2. 运行 MLflow 教程项目，训练[酒品模型](https://github.com/mlflow/mlflow/tree/master/examples/sklearn_elasticnet_wine)。 将 `<experiment-id>` 替换为上一步中记下的试验 ID。

   ```bash
   mlflow run https://github.com/mlflow/mlflow#examples/sklearn_elasticnet_wine -b databricks --backend-config cluster-spec.json --experiment-id <experiment-id>
   ```

   ```console
   === Fetching project from https://github.com/mlflow/mlflow#examples/sklearn_elasticnet_wine into /var/folders/kc/l20y4txd5w3_xrdhw6cnz1080000gp/T/tmpbct_5g8u ===
   === Uploading project to DBFS path /dbfs/mlflow-experiments/<experiment-id>/projects-code/16e66ccbff0a4e22278e4d73ec733e2c9a33efbd1e6f70e3c7b47b8b5f1e4fa3.tar.gz ===
   === Finished uploading project to /dbfs/mlflow-experiments/<experiment-id>/projects-code/16e66ccbff0a4e22278e4d73ec733e2c9a33efbd1e6f70e3c7b47b8b5f1e4fa3.tar.gz ===
   === Running entry point main of project https://github.com/mlflow/mlflow#examples/sklearn_elasticnet_wine on Databricks ===
   === Launched MLflow run as Databricks job run with ID 8651121. Getting run status page URL... ===
   === Check the run's status at https://<databricks-instance>#job/<job-id>/run/1 ===
   ```

3. 复制 MLflow 运行输出的最后一行中的 URL `https://<databricks-instance>#job/<job-id>/run/1`。

### <a name="step-3-view-the-azure-databricks-job-run"></a>步骤 3：查看 Azure Databricks 作业运行

1. 在浏览器中打开在上一步中复制的 URL 以查看 Azure Databricks 作业运行输出：

   > [!div class="mx-imgBorder"]
   > ![作业运行输出](../../_static/images/mlflow/mlflow-job-run.png)

### <a name="step-4-view-the-experiment-and-mlflow-run-details"></a>步骤 4：查看试验和 MLflow 运行详细信息

1. 导航到 Azure Databricks 工作区中的试验。

   > [!div class="mx-imgBorder"]
   > ![转向试验](../../_static/images/mlflow/mlflow-workspace-experiment.png)

2. 单击试验。

   > [!div class="mx-imgBorder"]
   > ![查看试验](../../_static/images/mlflow/mlflow-experiment.png)

3. 若要显示运行详细信息，请单击 Date 列中的链接。

   > [!div class="mx-imgBorder"]
   > ![运行详细信息](../../_static/images/mlflow/mlflow-run-remote.png)

通过单击作业输出字段中的日志链接，可以查看运行中的日志。

## <a name="resources"></a>资源

如需了解一些 MLflow 项目示例，请参阅 [MLflow 应用库](https://github.com/mlflow/mlflow-apps)，该库包含一个现成的项目存储库，旨在使代码中易于包含 ML 功能。