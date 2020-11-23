---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/15/2020
title: 跨工作区共享模型 - Azure Databricks
description: 了解如何跨多个工作区使用模型注册表。
ms.openlocfilehash: 29f367ebc93ee8f07a0484ede9371b49228f0318
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589665"
---
# <a name="share-models-across-workspaces"></a>跨工作区共享模型

Azure Databricks 支持在多个工作区之间共享模型。 例如，你可以在自己的工作区中开发和记录模型，然后将其注册到集中式模型注册表。 多个团队共享对模型的访问权限或组织有多个工作区来处理不同的开发阶段时，这非常有用。

在这种情况下，Azure Databricks 建议创建一个专用工作区来保存集中式模型注册表，其中包含每个需要访问的用户的帐户。 其中包括记录和注册模型的数据科学家以及管理和部署模型的生产用户，如图中的多工作区设置示例所示。

> [!div class="mx-imgBorder"]
> ![多个工作区](../../_static/images/mlflow/multiworkspace.png)

可以通过 [MLflow Python 客户端](model-registry.md#model-registry-api-workflow)访问集中式注册，并通过令牌进行控制。 每个需要访问的用户或脚本都会在集中式注册中[创建个人访问令牌](../../dev-tools/api/latest/authentication.md#token-management)，并[将该令牌复制到其他工作区的机密管理器中](../../dev-tools/cli/secrets-cli.md)。 发送到集中式注册表工作区的每个 API 请求必须包含访问令牌；MLflow 提供了一个简单的机制来指定在执行模型注册表操作时要使用的机密。

模型注册表的所有客户端和 Fluent API 方法都支持远程工作区。

## <a name="requirements"></a>要求

跨工作区使用模型注册表需要 MLflow Python 客户端，版本 1.11.0 或以上。

## <a name="set-up-the-api-token-for-a-remote-registry"></a>设置远程注册表的 API 令牌

1. 在模型注册表工作区中[创建访问令牌](../../dev-tools/api/latest/authentication.md#token-management)。
2. 在本地工作区中，[创建机密](../../dev-tools/cli/secrets-cli.md)来存储访问令牌和远程工作区信息：
   1. 创建机密范围：`databricks secrets create-scope --scope <scope>`。
   1. 为目标工作区选取唯一名称，此处显示为 `<prefix>`。 然后[创建](../../dev-tools/cli/secrets-cli.md#create-a-secret-scope)三个机密：
      * `databricks secrets put --scope <scope> --key <prefix>-host`：输入模型注册表工作区的主机名。 例如，`https://chinaeast.databricks.azure.cn/` 或 `https://adb-5555555555555555.19.databricks.azure.cn/`。
      * `databricks secrets put --scope <scope> --key <prefix>-token`：输入模型注册表工作区的访问令牌。
      * `databricks secrets put --scope <scope> --key <prefix>-workspace-id`：输入模型注册表工作区的工作区 ID，其可以在任意页面的 [URL 中查找](../../workspace/workspace-details.md#workspace-instance-names-urls-and-ids)。

> [!NOTE]
>
> 你可能想要与其他用户[共享机密范围](../../security/access-control/secret-acl.md)，因为[每个工作区的机密范围数量有限](../../security/secrets/secret-scopes.md)。

## <a name="specifying-a-remote-registry"></a>指定远程注册表

根据为远程注册表工作区创建的机密范围和名称前缀，可以构造以下格式的注册表 URI：

```python
registry_uri = f'databricks://{scope}:{prefix}'
```

URI 可用于通过首次调用来指定 Fluent API 方法的远程注册表：

```python
mlflow.set_registry_uri(registry_uri)
```

也可以在实例化 `MlflowClient` 时中显式指定：

```python
client = MlflowClient(registry_uri=registry_uri)
```

以下工作流演示了这两种方法的示例。

## <a name="register-a-model-in-the-remote-registry"></a>在远程注册表中注册模型

注册模型的一种方法是使用 `mlflow.register_model` API：

```python
mlflow.set_registry_uri(registry_uri)
mlflow.register_model(model_uri=f'runs:/{run_id}/{artifact_path}', name=model_name)
```

其他模型注册方法的示例可以在本页末尾的笔记本中找到。

> [!NOTE]
>
> 在远程工作区中注册模型时，会在远程工作区中创建 DBFS 中的模型项目的临时副本。 模型版本转为 `READY` 状态后，你可能需要删除此副本。 可以在 `/dbfs/databricks/mlflow/tmp-external-source/<run_id>` 文件夹下找到临时文件。

你还可以指定 `tracking_uri`，以指向 `registry_uri` 的类似方式指向另一个工作区中的 MLflow 跟踪服务。 这意味着可以在远程工作区上运行，并在当前或其他远程工作区中注册其模型。

## <a name="use-a-model-from-the-remote-registry"></a>使用远程注册表中的模型

可通过先设置注册表 URI，使用 `mlflow.<flavor>.load_model` 方法在远程注册表中加载和使用模型版本：

```python
mlflow.set_registry_uri(registry_uri)
model = mlflow.pyfunc.load_model(f'models://{model_name}/Staging')
model.predict(...)
```

或可以在 `models:/` URI 中显式指定远程注册表：

```python
model = mlflow.pyfunc.load_model(f'models://{scope}:{prefix}@databricks/{model_name}/Staging')
model.predict(...)
```

还支持用于访问模型文件的其他帮助器方法，例如：

```python
client.get_latest_versions(model_name)
client.get_model_version_download_uri(model_name, version)
```

## <a name="manage-a-model-in-the-remote-registry"></a>在远程注册表中管理模型

只要具有所需的权限，就可以对远程注册表中的模型执行任何操作。 例如，如果对模型具有“可以管理”权限，则可以使用 `MlflowClient` 方法转换模型版本阶段或删除模型：

```python
client = MlflowClient(tracking_uri=None, registry_uri=registry_uri)
client.transition_model_version_stage(model_name, version, 'Archived')
client.delete_registered_model(model_name)
```

## <a name="notebook"></a>笔记本

### <a name="centralized-model-registry-example-notebook"></a>集中式模型注册表示例笔记本

[获取笔记本](../../_static/notebooks/mlflow/mlflow-model-registry-multi-workspace.html)