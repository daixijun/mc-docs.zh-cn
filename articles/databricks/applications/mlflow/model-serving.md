---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 10/01/2020
title: Azure Databricks 上的 MLflow 模型服务 - Azure Databricks
description: 了解如何通过 Azure Databricks 模型服务将 MLflow 模型部署为 REST API 终结点。
ms.openlocfilehash: 2a311d762df48b339b052195a83625c352595e46
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589667"
---
# <a name="mlflow-model-serving-on-azure-databricks"></a><a id="mlflow-model-serving"> </a><a id="mlflow-model-serving-on-azure-databricks"> </a>Azure Databricks 上的 MLflow 模型服务

> [!IMPORTANT]
>
> 此功能目前以[公共预览版](../../release-notes/release-types.md)提供。

MLflow 模型服务允许你将模型注册表中的机器学习模型作为 REST 终结点（它们基于模型版本的可用性和阶段自动更新）托管。

为给定的已注册模型启用模型服务时，Azure Databricks 会自动为该模型创建唯一的单节点群集，并在该群集上部署该模型的所有未存档版本。 如果出现任何错误，Azure Databricks 会重启群集，并在你为模型禁用模型服务的情况下终止群集。 模型服务自动与模型注册表同步，并部署任何新的已注册模型版本。 可以通过标准 REST API 请求查询已部署的模型版本。 Azure Databricks 使用其标准身份验证对针对模型的请求进行身份验证。

当此服务为预览版时，Databricks 建议将其用于低吞吐量和非关键应用程序。 目标吞吐量为 20 qps，目标可用性为 99.5%，但任何一个都不保证能达到。 此外，有效负载大小限制为每个请求 16 MB。

每个模型版本都使用 MLflow [模型部署](https://www.mlflow.org/docs/latest/models.html#deploy-mlflow-models)进行部署，并在其依赖项指定的 Conda 环境中运行。

> [!NOTE]
>
> 只要启用了服务，就会维护群集，即使不存在任何活动的模型版本。 若要终止服务群集，请为已注册的模型禁用模型服务。

## <a name="requirements"></a>要求

MLflow 模型提供功能可用于 Python MLflow 模型。 所有模型依赖项都必须在 conda 环境中声明。

## <a name="enable-and-disable-model-serving"></a>启用和禁用模型服务

可以从[已注册模型页](model-registry.md#registered-model-page)为模型启用服务。

1. 单击“服务”选项卡。如果尚未为模型启用服务，则会显示“启用服务”按钮。
2. 单击“启用服务”。 将显示“服务”选项卡，其中的“状态”为“挂起”。 几分钟后，“状态”更改为“就绪”。

若要为模型禁用服务，请单击“停止”。

## <a name="model-serving-from-model-registry"></a>模型注册表中的模型服务

你可以在模型注册表 UI 中启用已注册模型的服务。

> [!div class="mx-imgBorder"]
> ![启用服务](../../_static/images/mlflow/enable-serving.gif)

### <a name="model-version-uris"></a>模型版本 URI

每个已部署的模型版本都分配有一个或多个唯一 URI。 每个模型版本至少分配有一个如下构造的 URI：

`<databricks-instance>/model/<registered-model-name>/<model-version>/invocations`

例如，若要调用注册为 `iris-classifier` 的模型的版本 1，请使用以下 URI：

`https://<databricks-instance>/model/iris-classifier/1/invocations`

还可以通过模型版本的阶段来调用模型版本。 例如，如果版本 1 处于“生产”阶段，则还可以使用以下 URI 对其进行评分：

`https://<databricks-instance>/model/iris-classifier/Production/invocations`

可用模型 URI 的列表显示在服务页上的“模型版本”选项卡的顶部。

## <a name="manage-served-versions"></a>管理所服务的版本

所有活动的（未存档的）模型版本都会部署，你可以使用 URI 查询它们。 Azure Databricks 在新的模型版本注册时自动部署新版本，并在旧版本存档时自动删除旧版本。

> [!NOTE]
>
> 已注册模型的所有已部署版本共享同一群集。

## <a name="manage-model-access-rights"></a>管理模型访问权限

模型访问权限从模型注册表继承而来。 启用或禁用服务功能需要对已注册的模型拥有“管理”权限。 具有读取权限的任何人都可以对任何已部署的版本进行评分。

## <a name="score-deployed-model-versions"></a>对已部署的模型版本进行评分

若要对已部署的模型进行评分，可以使用 UI 或将 REST API 请求发送到模型 URI。

### <a name="score-via-ui"></a>通过 UI 进行评分

这是用来测试模型的最简单且最快速的方法。 你可以插入 JSON 格式的模型输入数据，并单击“发送请求”。 如果已使用输入示例记录了模型（如上图所示），请单击“加载示例”来加载输入示例。

### <a name="score-via-rest-api-request"></a>通过 REST API 请求进行评分

你可以使用[标准 Databricks 身份验证](../../dev-tools/api/latest/authentication.md)通过 REST API 发送评分请求。 下面的示例演示了如何使用个人访问令牌进行身份验证。

对于给定的 `MODEL_VERSION_URI`（例如 `https://<databricks-instance>/model/iris-classifier/Production/invocations`，其中的 `<databricks-instance>` 是[你的 Databricks 实例的名称](../../workspace/workspace-details.md#workspace-instance-names-urls-and-ids)）和名为 `DATABRICKS_API_TOKEN` 的 Databricks REST API 令牌，下面提供了一些示例代码片段来说明如何查询所服务的模型：

#### <a name="bash"></a>Bash

```bash
curl -u token:$DATABRICKS_API_TOKEN $MODEL_VERSION_URI \
  -H 'Content-Type: application/json; format=pandas-records' \
  -d '[
    {
      "sepal_length": 5.1,
      "sepal_width": 3.5,
      "petal_length": 1.4,
      "petal_width": 0.2
    }
  ]'
```

#### <a name="python"></a>Python

```python
import requests

def score_model(model_uri, databricks_token, data):
  headers = {
    "Authorization": f"Bearer {databricks_token}",
    "Content-Type": "application/json; format=pandas-records",
  }
  data_json = data if isinstance(data, list) else data.to_dict(orient="records")
  response = requests.request(method='POST', headers=headers, url=model_uri, json=data_json)
  if response.status_code != 200:
      raise Exception(f"Request failed with status {response.status_code}, {response.text}")
  return response.json()

data = [{
  "sepal_length": 5.1,
  "sepal_width": 3.5,
  "petal_length": 1.4,
  "petal_width": 0.2
}]
score_model(MODEL_VERSION_URI, DATABRICKS_API_TOKEN, data)

# can also score DataFrames
import pandas as pd
score_model(MODEL_VERSION_URI, DATABRICKS_API_TOKEN, pd.DataFrame(data))
```

#### <a name="powerbi"></a>Powerbi

可以使用以下步骤在 Power BI Desktop 中为数据集评分：

1. 打开要评分的数据集。
2. 转到“转换数据”。
3. 右键单击左侧面板，然后选择“创建新查询”。
4. 转到“视图”>“高级编辑器”。
5. 填写适当的 `DATABRICKS_API_TOKEN` 和 `MODEL_VERSION_URI` 后，将查询主体替换为下面的代码片段。

   ```
   (dataset as table ) as table =>
   let
     call_predict = (dataset as table ) as list =>
     let
       apiToken = DATABRICKS_API_TOKEN,
       modelUri = MODEL_VERSION_URI,
       responseList = Json.Document(Web.Contents(modelUri,
         [
           Headers = [
             #"Content-Type" = "application/json; format=pandas-records",
             #"Authorization" = Text.Format("Bearer #{0}", {apiToken})
           ],
           Content = Json.FromValue(dataset)
         ]
       ))
     in
       responseList,
     predictionList = List.Combine(List.Transform(Table.Split(dataset, 256), (x) => call_predict(x))),
     predictionsTable = Table.FromList(predictionList, (x) => {x}, {"Prediction"}),
     datasetWithPrediction = Table.Join(
       Table.AddIndexColumn(predictionsTable, "index"), "index",
       Table.AddIndexColumn(dataset, "index"), "index")
   in
     datasetWithPrediction
   ```

6. 将查询命名为所需的模型名称。
7. 打开你的数据集的高级查询编辑器，并应用模型函数。

若要详细了解服务器接受的输入数据格式（例如，面向 pandas 拆分的格式），请参阅 [MLflow 文档](https://www.mlflow.org/docs/latest/models.html#deploy-mlflow-models)。

## <a name="monitor-served-models"></a>监视所服务的模型

服务页显示服务群集的状态指示器以及各个模型版本。 此外，还可以使用以下方式获取更多信息：

* 若要检查服务群集的状态，请使用“模型事件”选项卡，其中显示了此模型的所有服务事件的列表。
* 若要检查单个模型版本的状态，请使用“模型版本”选项卡上的“日志”或“版本事件”选项卡。

> [!div class="mx-imgBorder"]
> ![版本状态](../../_static/images/mlflow/version_status.png)

> [!div class="mx-imgBorder"]
> ![模型事件](../../_static/images/mlflow/model_events.png)