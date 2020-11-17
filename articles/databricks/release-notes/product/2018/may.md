---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 2018 年 5 月 - Azure Databricks
description: Azure Databricks 新功能和改进的 2018 年 5 月发行说明。
ms.openlocfilehash: 8fd8daae09ddd183684f6d953c0160021c59f44b
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329233"
---
# <a name="may-2018"></a>2018 年 5 月

发布分阶段进行。 Azure Databricks 帐户可能要等到初始发布日期后的一周内才会更新。

## <a name="general-data-protection-regulation-gdpr"></a>一般数据保护条例 (GDPR)

2018 年 5 月 24 日：版本 2.72

为了满足 2018 年 5 月 25 日生效的欧盟一般数据保护条例 (GDPR) 的要求，我们对 Azure Databricks 平台进行了多次修改，以便用户更好地控制帐户和用户级别的数据保留。 更新包括：

* 群集删除：使用 UI 或群集 API 永久删除群集配置。 请参阅[删除群集](../../../clusters/clusters-manage.md#cluster-delete)。
* 工作区清除（在版本 2.71 中发布）：永久删除工作区对象，如整个笔记本、单个笔记本单元格、单个笔记本注释和笔记本修订历史记录。 请参阅[管理工作区存储](../../../administration-guide/workspace/storage.md)。
* 笔记本修订历史记录清除：
  * 在定义的时间范围内永久删除工作区中所有笔记本的修订历史记录。 请参阅[管理工作区存储](../../../administration-guide/workspace/storage.md)。
  * 永久删除单个笔记本修订或笔记本的整个修订历史记录。 请参阅[版本控制](../../../notebooks/notebooks-use.md#version-control)。

有关删除 Azure Databricks 服务或取消 Azure 帐户的信息，请参阅[管理订阅](../../../administration-guide/account-settings/account.md)。

## <a name="azure-databricks-users-must-belong-to-azure-ad-tenant"></a>Azure Databricks 用户必须属于 Azure AD 租户

2018 年 5 月 24 日：版本 2.72

现在，仅当用户属于 Azure Databricks 工作区的 [Azure Active Directory (Azure AD) 租户](/architecture/cloud-adoption-guide/adoption-intro/tenant-explainer)时，才能登录到 Azure Databricks。 如果用户不属于 Azure AD 租户，可以[将他们添加为标准或来宾用户](/active-directory/add-users-azure-active-directory)。

## <a name="horovodestimator"></a>HorovodEstimator

2018 年 5 月 29 日：版本 2.72

添加了 HorovodEstimator 的文档和笔记本，HorovodEstimator 是一种 MLlib 样式的评估器 API，它利用 Uber 的 [Horovod](https://github.com/horovod/horovod) 框架。 HorovodEstimator 可以辅助 Spark DataFrame 上深度神经网络的分布式、多 GPU 训练，从而简化 Spark 中的 ETL 与 TensorFlow 中的模型训练的集成。 请参阅 [HorovodEstimator：使用 Horovod 和 Apache Spark MLlib 进行分布式深度学习](../../../applications/machine-learning/train-model/distributed-training/horovod-estimator.md)。

## <a name="mleap-ml-model-export"></a>MLeap ML 模型导出

2018 年 5 月 24 日：版本 2.72

添加了有关在 Azure Databricks 上使用 MLeap 的文档和笔记本。 通过 MLeap，可以将机器学习管道从 Apache Spark 和 cikit-learn 部署到可移植格式和执行引擎。 请参阅 [MLeap ML 模型导出](../../../applications/machine-learning/model-export/mleap-model-export.md)。

## <a name="even-more-gpu-cluster-types"></a>更多的 GPU 群集类型

2018 年 5 月 24 日：版本 2.72

除了在版本 2.71 中添加的 Azure NC 实例类型（NC12 和 NC24），我们现在还支持 Azure Databricks 群集上的 NCv3 实例类型系列（NC6s_v3、NC12s_v3 和 NC24s_v3）  。 NC 和 NCv3 实例提供 GPU，以支持图像处理、文本分析和其他机器学习和深度学习任务，这些任务在计算上具有挑战性，需要卓越的性能。

请参阅[启用了 GPU 的群集](../../../clusters/gpu.md#gpu-clusters)。

## <a name="notebook-cells-hide-and-show"></a>笔记本单元：隐藏和显示

2018 年 5 月 24 日：版本 2.72

新的指示器和消息使隐藏后的笔记本单元格内容更易于显示。 请参阅[隐藏和显示单元格内容](../../../notebooks/notebooks-use.md#hide-show-cell)。

## <a name="doc-site-search"></a>文档站点搜索

**2018 年 5 月 22 日**

我们已经用更好的搜索工具替换了文档站点搜索。 在接下来的几周内，你将会看到更多的搜索改进。

> [!NOTE]
>
> 如果在新搜索部署后不久尝试搜索，搜索可能看起来是损坏的。 只需清除浏览器缓存，即可看到新的搜索体验。

## <a name="databricks-runtime-41-ml-for-machine-learning-beta"></a>用于机器学习的 Databricks Runtime 4.1 ML (Beta)

2018 年 5 月 17 日

Databricks Runtime ML (Beta) 为机器学习和数据科学提供了随时可用的环境。 它包含多个热门库，其中包括 TensorFlow、Keras 和 XGBoost。

通过 Databricks Runtime ML，可启动具有分布式 TensorFlow 训练所需的所有库的 Databricks 群集。 它可确保群集中包含的库的兼容性（例如 TensorFlow 和 CUDA / cuDNN 之间的兼容性），并且与使用 init 脚本相比，显著缩短了群集启动时间。

> [!NOTE]
>
> Databricks Runtime 4.1 ML 仅适用于高级 SKU。

请参阅 [Databricks Runtime 4.1 ML (Beta)](../../runtime/4.1ml.md) 的完整发行说明。

## <a name="databricks-delta"></a>Databricks Delta

2018 年 5 月 17 日

Databricks Delta 现在面向 Azure Databricks 用户推出个人预览版。  请联系你的帐户管理员或在 [https://databricks.com/product/databricks-delta](https://databricks.com/product/databricks-delta) 进行注册。 此版本表示即将正式发布的正式版的候选版本。

有关详细信息，请参阅 [Databricks Runtime 4.1](../../runtime/4.1.md) 和 [Delta Lake](../../../delta/index.md)。

## <a name="display-support-for-image-data-types"></a>图像数据类型的 Display() 支持

2018 年 5 月 17 日

在 Databricks Runtime 4.1 中，`display()` 现以富 HTML 的形式呈现包含图像数据类型的列。

请参阅[图像](../../../notebooks/visualizations/index.md#display-image-type)。

## <a name="gpu-cluster-types"></a>GPU 群集类型

2018 年 5 月 15 日：版本 2.71

我们很高兴地宣布推出对 Azure Databricks 群集上的 Azure NC 实例类型（NC12 和 NC24）的支持。 NC 实例提供 GPU，以支持图像处理、文本分析和其他机器学习和深度学习任务，这些任务在计算上具有挑战性，需要卓越的性能。

Azure Databricks 还提供为 GPU 配置的预安装 NVIDIA 驱动程序和库，以及有助于开始使用若干热门的深度学习库的材料。

另请参阅：

* [支持 GPU 的群集](../../../clusters/gpu.md#gpu-clusters)
* [机器学习和深度学习](../../../applications/machine-learning/index.md)

## <a name="secret-management-ga"></a>机密管理正式版

2018 年 5 月 15 日：版本 2.71

以前处于个人预览阶段的机密管理现已正式发布。 它提供了功能强大的工具，用于管理验证外部数据源所需的凭据。 使用 Databricks 机密管理在笔记本和作业中存储和引用凭据，而不是直接在笔记本中键入凭据。 若要管理机密，可以使用[机密 CLI](../../../dev-tools/cli/secrets-cli.md) 访问[机密 API](../../../dev-tools/api/latest/secrets.md)。

> [!NOTE]
>
> 机密管理要求安装 Databricks Runtime 4.0 或更高版本，以及 Databricks CLI 0.7.1 或更高版本。

请参阅[机密管理](../../../security/secrets/index.md#secrets-user-guide)。

## <a name="secrets-api-endpoint-and-cli-command-changes"></a>机密 API 终结点和 CLI 命令更改

2018 年 5 月 15 日：版本 2.71

对机密 API 终结点进行了以下更改：

* 对于所有终结点，根路径已从 `/secret` 更改为 `/secrets`。
* 对于机密终结点，`/secret/secrets` 已折叠为 `/secrets/`。
* `write` 方法已更改为 `put`。

Databricks CLI 0.7.1 包括对机密命令的更新，以与这些更新的 API 终结点保持一致。

请参阅[机密 API](../../../dev-tools/api/latest/secrets.md) 和[机密管理](../../../security/secrets/index.md#secrets-user-guide)。

## <a name="cluster-pinning"></a>群集固定

2018 年 5 月 15 日：版本 2.71

现在可以将群集固定到群集列表。 这样你能保留已终止 30 天以上的群集的配置。

> [!div class="mx-imgBorder"]
> ![固定群集](../../../_static/images/clusters/pin-list.png)

此外，“群集”页现在显示 30 天（以前是 7 天）内终止的所有群集。

请参阅[固定群集](../../../clusters/clusters-manage.md#cluster-pin)。

## <a name="cluster-autostart"></a>群集自动启动

2018 年 5 月 15 日：版本 2.71

在此版本之前，计划在 `Terminated` 群集上运行的作业失败。 对于在 Azure Databricks 版本 2.71 及以上版本中创建的群集，来自 JDBC/ODBC 接口的命令或分配给现有终止群集的作业运行会自动重启该群集。  请参阅 [JDBC 连接](../../../integrations/bi/jdbc-odbc-bi.md#cluster-requirements)和[创建作业](../../../jobs.md#job-create)。

通过自动启动，可以将群集配置为自动终止，而无需手动干预来为计划的作业重启群集。 此外，还可以通过计划在指定的时间重启已终止的群集的作业来计划群集初始化。

强制实施群集访问控制，并照常检查作业所有者权限。

## <a name="workspace-purging"></a>工作区清除

2018 年 5 月 15 日：版本 2.71

在始终努力遵循欧盟一般数据保护条例 (GDPR) 的过程中，我们添加了清除工作区对象（如整个笔记本、单个笔记本单元格、单个笔记本注释和笔记本修订历史记录）的功能。 几周后，我们将发布更多功能和文档，以支持 GDPR 符合性。

请参阅[管理工作区存储](../../../administration-guide/workspace/storage.md)。

## <a name="databricks-cli-071"></a>Databricks CLI 0.7.1

2018 年 5 月 10 日

Databricks CLI 0.7.1 包括对“机密”命令的更新，以与更新的 API 终结点保持一致。

请参阅 [Databricks CLI](../../../dev-tools/cli/index.md) 和[机密管理](../../../security/secrets/index.md#secrets-user-guide)。