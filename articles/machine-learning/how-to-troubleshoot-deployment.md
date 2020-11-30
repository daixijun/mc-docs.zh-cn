---
title: Web 服务部署故障排除
titleSuffix: Azure Machine Learning
description: 了解如何规避、解决及排查 Azure Kubernetes 服务和 Azure 容器实例的常见 Docker 部署错误。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.reviewer: jmartens
ms.date: 11/02/2020
ms.topic: troubleshooting
ms.custom: contperfq4, devx-track-python, deploy
ms.openlocfilehash: 319471244d46cd69e26059c0448293120fb54493
ms.sourcegitcommit: c2c9dc65b886542d220ae17afcb1d1ab0a941932
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94977220"
---
# <a name="troubleshoot-model-deployment"></a>排查模型部署问题

了解如何使用 Azure 机器学习排查、解决或规避 Azure 容器实例 (ACI) 和 Azure Kubernetes 服务 (AKS) 的常见 Docker 部署错误。

## <a name="prerequisites"></a>先决条件

* 一个 **Azure 订阅**。 如果没有订阅，可试用 [Azure 机器学习试用版](https://www.azure.cn/pricing/1rmb-trial)。
* [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)。
* [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)。
* [用于 Azure 机器学习的 CLI 扩展](reference-azure-machine-learning-cli.md)。
* 若要在本地调试，则必须在本地系统上安装一个有效的 Docker。

    若要验证 Docker 安装，请使用终端或命令提示符中的命令 `docker run hello-world`。 有关安装 Docker 或排除 Dcoker 错误的详细信息，请参阅 [Docker 文档](https://docs.docker.com/)。

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>机器学习模型的 Docker 部署步骤

在 Azure 机器学习中将模型部署到非本地计算时，会发生以下情况：

1. 你在 InferenceConfig 的 Environments 对象中指定的 Dockerfile 将与源目录的内容一起发送到云
1. 如果以前生成的映像在容器注册表中不可用，则云中会生成新的 Docker 映像，该映像会存储在工作区的默认容器注册表中。
1. 容器注册表中的 Docker 映像将下载到计算目标。
1. 工作区的默认 Blob 存储会装载到计算目标，使你可以访问已注册的模型
1. Web 服务器通过运行入口脚本的 `init()` 函数进行初始化
1. 已部署的模型收到请求时，`run()` 函数会处理该请求

使用本地部署时的主要区别是，容器映像在本地计算机上生成，因此需要为本地部署安装 Docker。

了解这些高级步骤会有助于了解发生错误的位置。

## <a name="get-deployment-logs"></a>获取部署日志

调试错误的第一步是获取部署日志。 首先，请按照[此处的说明](how-to-deploy-and-where.md#connect-to-your-workspace)连接到工作区。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

若要从已部署的 Web 服务获取日志，请执行以下命令：

```bash
az ml service get-logs --verbose --workspace-name <my workspace name> --name <service name>
```

# <a name="python"></a>[Python](#tab/python)


假设有一个名为 `ws` 的 `azureml.core.Workspace` 类型的对象，可以执行以下操作：

```python
print(ws.webservices)

# Choose the webservice you are interested in

from azureml.core import Webservice

service = Webservice(ws, '<insert name of webservice>')
print(service.get_logs())
```

---

## <a name="debug-locally"></a>本地调试

如果将模型部署到 ACI 或 AKS 时遇到问题，请将其部署为本地 Web 服务。 使用本地 Web 服务可简化解决问题的过程。

可以在 [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) 存储库中找到示例[本地部署笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb)，以探索可运行的示例。

> [!WARNING]
> 生成方案不支持本地 Web 服务部署。

若要进行本地部署，请修改代码以使用 `LocalWebservice.deploy_configuration()` 创建部署配置。 然后使用 `Model.deploy()` 部署该服务。 以下示例将模型（包含在 model 变量中）部署为本地 Web 服务：

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice

# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

如果定义你自己的 conda 规范 YAML，请将版本大于等于 1.0.45 的 azureml-defaults 作为 pip 依赖项列出。 需要此包以将模型作为 Web 服务托管。

此时，你可以正常使用该服务。 以下代码演示了将数据发送到该服务的过程：

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

有关自定义 Python 环境的详细信息，请参阅[创建和管理用于训练和部署的环境](how-to-use-environments.md)。 

### <a name="update-the-service"></a>更新服务

在本地测试中，可能需要更新 `score.py` 文件以添加记录或尝试解决发现的任何问题。 若要重新加载对 `score.py` 文件的更改，请使用 `reload()`。 例如，以下代码重新加载服务的脚本，然后向其发送数据。 使用 `score.py` 文件对数据进行评分：

> [!IMPORTANT]
> `reload` 方法仅适用于本地部署。 有关将部署更新到其他计算目标的信息，请参阅[如何更新 webservice](how-to-deploy-update-web-service.md)。

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> 可从服务所使用的 `InferenceConfig` 对象指定的位置重新加载该脚本。

若要更改模型、Conda 依赖项或部署配置，请使用 [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueupdate--args-)。 以下示例更新服务使用的模型：

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>删除服务

要删除服务，请使用 [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--)。

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> 检查 Docker 日志

可以通过服务对象打印详细的 Docker 引擎日志消息。 可以查看 ACI、AKS 和 Local 部署的日志。 以下示例演示如何打印日志。

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```
如果在日志中看到行 `Booting worker with pid: <pid>` 多次出现，则意味着没有足够的内存来启动辅助角色。
可以通过增加 `deployment_config` 中 `memory_gb` 的值来处理错误
 
## <a name="container-cannot-be-scheduled"></a>无法计划容器

将服务部署到 Azure Kubernetes Service 计算目标时，Azure 机器学习将尝试使用请求的资源量来计划服务。 如果在 5 分钟后，群集中没有具有相应可用资源量的节点，则部署将失败。 失败消息为 `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00`。 可通过添加更多节点、更改节点的 SKU 或更改服务的资源要求来解决此错误。 

该错误消息通常会指示你更需要哪一种资源 - 例如，如果看到一条指示“`0/3 nodes are available: 3 Insufficient nvidia.com/gpu`”的错误消息，则意味着该服务需要 GPU，且群集中有三个节点没有可用的 GPU。 如果使用的是 GPU SKU，则可以通过添加更多节点来解决此问题；如果使用的不是 GPU SKU，则可以通过切换到启用 GPU 的 SKU，或将环境更改为不需要 GPU 来解决此问题。  

## <a name="service-launch-fails"></a>服务启动失败

成功生成映像后，系统会尝试使用部署配置启动容器。 作为容器启动过程的一部分，评分脚本中的 `init()` 函数由系统调用。 如果 `init()` 函数中存在未捕获的异常，则可能在错误消息中看到 CrashLoopBackOff 错误。

使用[检查 Docker 日志](#dockerlog)部分中的信息检查日志。

## <a name="function-fails-get_model_path"></a>函数故障：get_model_path()

通常情况下，在评分脚本的 `init()` 函数中，会调用 [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) 来查找容器中的模型文件或模型文件的文件夹。 如果找不到模型文件或文件夹，函数将失败。 调试此错误的最简单方法是在容器 shell 中运行以下 Python 代码：

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

该示例将打印容器中的本地路径（相对于 `/var/azureml-app`），其中评分脚本有望找到模型文件或文件夹。 然后可以验证文件或文件夹是否确实位于其应该在的位置。

将日志记录级别设置为 DEBUG 可能会导致记录其他信息，这可能有助于识别故障。

## <a name="function-fails-runinput_data"></a>函数故障：run(input_data)

如果服务部署成功，但在向评分终结点发布数据时崩溃，可在 `run(input_data)` 函数中添加错误捕获语句，以便转而返回详细的错误消息。 例如：

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**注意**：通过 `run(input_data)` 调用返回错误消息应仅用于调试目的。 出于安全原因，不应在生产环境中按此方法返回错误消息。

## <a name="http-status-code-502"></a>HTTP 状态代码 502

502 状态代码指示服务在 score.py 文件的 `run()` 方法中引发了异常或已崩溃。 使用本文中的信息调试该文件。

## <a name="http-status-code-503"></a>HTTP 状态代码 503

Azure Kubernetes 服务部署支持自动缩放，这允许添加副本以支持额外的负载。 自动缩放程序旨在处理负载中的逐步更改。 如果每秒收到大量请求，客户端可能会收到 HTTP 状态代码 503。 即使自动缩放器反应迅速，但 AKS 仍需要大量时间来创建其他容器。

纵向扩展/缩减决策取决于当前容器副本的利用率。 处于繁忙状态（正在处理请求）的副本数除以当前副本的总数为当前利用率。 如果此数字超出 `autoscale_target_utilization`，则会创建更多的副本。 如果它较低，则会减少副本。 添加副本的决策是迫切而迅速的（大约 1 秒）。 删除副本的决策是保守的（大约 1 分钟）。 默认情况下，自动缩放目标利用率设置为 70%，这意味着服务可以处理高达 30% 的大量每秒请求数 (RPS) 。

有两种方法可以帮助防止 503 状态代码：

> [!TIP]
> 这两种方法可以单独使用，也可以结合使用。

* 更改自动缩放创建新副本的利用率。 可以通过将 `autoscale_target_utilization` 设置为较低的值来调整利用率目标。

    > [!IMPORTANT]
    > 该更改不会导致更快创建副本。 而会以较低的利用率阈值创建副本。 可以在利用率达到 30% 时，通过将值改为 30% 来创建副本，而不是等待该服务的利用率达到 70% 时再创建。
    
    如果 Web 服务已在使用当前最大副本，然后你仍能看见 503 状态代码，则请增加 `autoscale_max_replicas` 值以增加副本的最大数量。

* 更改副本最小数量。 增加最小副本可提供一个更大的池来处理传入峰值。

    若要增加副本的最小数量，请将 `autoscale_min_replicas` 设置为更大的值。 可以使用以下代码计算所需的副本，将值替换为项目指定的值：

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > 如果收到请求高峰大于新的最小副本可以处理的数量，则可能会再次收到 503 代码。 例如，服务流量增加时，可能需要增加最小副本数据。

有关设置 `autoscale_target_utilization`、`autoscale_max_replicas` 和 `autoscale_min_replicas` 的详细信息，请参阅 [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?preserve-view=true&view=azure-ml-py) 模块参考。

## <a name="http-status-code-504"></a>HTTP 状态代码 504

504 状态代码指示请求已超时。默认超时值为 1 分钟。

可以通过修改 score.py 删除不必要的调用来增加超时值或尝试加快服务速度。 如果这些操作不能解决问题，请使用本文中的信息调试 score.py 文件。 代码可能处于无响应状态或无限循环。

## <a name="advanced-debugging"></a>高级调试

你可能需要以交互方式调试模型部署中包含的 Python 代码。 例如，如果输入脚本失败，并且无法通过其他记录确定原因。 通过使用 Visual Studio Code 和 debugpy，可以附加到在 Docker 容器中运行的代码。

有关详细信息，请访问[在 VS Code 指南中进行交互式调试](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments)。

## <a name="model-deployment-user-forum"></a>[模型部署用户论坛](https://docs.microsoft.com/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>后续步骤

详细了解部署：

* [部署方式和部署位置](how-to-deploy-and-where.md)
* [教程：训练和部署模型](tutorial-train-models-with-aml.md)
