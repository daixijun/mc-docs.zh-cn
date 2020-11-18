---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 10/02/2020
title: 2020 年 9 月 - Azure Databricks
description: 新 Azure Databricks 功能和改进的 2020 年 9 月发行说明。
ms.openlocfilehash: dd47e883f9d1dbecd721e49115f65e2d19f28de6
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329329"
---
# <a name="september-2020"></a>2020 年 9 月

这些功能和 Azure Databricks 平台的改进已于 2020 年 9 月发布。

> [!NOTE]
>
> 发布分阶段进行。 在初始发布日期后，可能最长需要等待一周，你的 Azure Databricks 帐户才会更新。

## <a name="databricks-runtime-73-73-ml-and-73-genomics-are-now-ga"></a>Databricks Runtime 7.3、7.3 ML 和 7.3 基因组学现已正式发布

**2020 年 9 月 24 日**

Databricks Runtime 7.3、Databricks Runtime 7.3 ML 和用于基因组学的 Databricks Runtime 7.3 现已正式发布。 它们带来了许多功能和改进，包括：

* Delta Lake 性能优化大大减少了开销
* 克隆指标
* Delta Lake `MERGE INTO` 改进
* 指定 Delta Lake 结构化流式处理的初始位置
* 自动加载程序改进
* 自适应查询执行
* Azure Synapse Analytics 连接器列长度控制
* 改进了 `dbutils.credentials.showRoles` 的行为
* 简化了 pandas 到 Spark 数据帧的转换
* `toPandas()` 调用中的新 `maxResultSize`
* pandas 和 PySpark UDF 的可调试性
* （仅限机器学习）辅助角色上的 Conda 激活
* （仅限基因组学）支持读取具有未压缩或 zstd 压缩基因型的 BGEN 文件
* 库升级

有关详细信息，请参阅 [Databricks Runtime 7.3](../../runtime/7.3.md)、[Databricks Runtime 7.3 ML](../../runtime/7.3ml.md) 和[用于基因组学的 Databricks Runtime 7.3](../../runtime/7.3genomics.md) 发行说明。

## <a name="single-node-clusters-public-preview"></a>单节点群集（公共预览版）

**2020 年 9 月 23-29 日：版本 3.29**

单节点群集是包含 Spark 驱动程序但不包含 Spark 工作器的群集。 相对而言，标准模式群集至少需要一个 Spark 工作器才能运行 Spark 作业。 单节点模式群集在以下情况下很有用：

* 运行需要 Spark 来加载和保存数据的单节点机器学习工作负荷
* 轻型探索性数据分析 (EDA)

有关详细信息，请参阅[单节点群集](../../../clusters/single-node.md)。

## <a name="dbfs-rest-api-rate-limiting"></a>DBFS REST API 速率限制

**2020 年 9 月 23-29 日：版本 3.29**

为了确保在负载较高的情况下也能提供高质量的服务，Azure Databricks 现在正针对 [DBFS API](../../../dev-tools/api/latest/dbfs.md) 调用强制实施 API 速率限制。 限制按工作区设置，以确保公平使用和高可用性。 如果使用 Databricks CLI 0.12.0 及更高版本，可以进行自动重试。 建议所有客户切换到最新的 Databricks CLI 版本。

## <a name="new-sidebar-icons"></a>新边栏图标

**2020 年 9 月 23-29 日**

我们已更新了 Azure Databricks 工作区 UI 中的边栏。 不是什么大问题，但我们认为新的图标看起来非常不错。

> [!div class="mx-imgBorder"]
> ![边栏](../../../_static/images/release-notes/new-sidebar-icons.png)

## <a name="running-jobs-limit-increase"></a>正在运行的作业限制增加

**2020 年 9 月 23-29 日：版本 3.29**

每个工作区的并发运行作业运行限制从 150 增加到了 1000。 超过 150 的运行将不再处于排队挂起状态。 当你请求不能立即启动的运行时，将返回 `429 Too Many Requests` 响应，而不是返回超出并发运行的运行请求的队列。 这种提高限制的措施已逐步推行，现已在所有区域中的所有工作区上可用。

## <a name="artifact-access-control-lists-acls-in-mlflow"></a>MLflow 中的项目访问控制列表 (ACL)

**2020 年 9 月 23-29 日：版本 3.29**

现在，对 MLflow 跟踪中的项目实施了 MLflow试验权限，使你可以轻松控制对模型、数据集和其他文件的访问。 默认情况下，当你创建新的试验时，其运行项目现在会存储在 MLflow 托管的一个位置。 将自动应用四个 MLflow 试验权限级别（**无权限**、**读取**、**编辑** 和 **管理**）来运行 MLflow 托管位置中存储的项目，如下所述：

* 若要将运行项目记录到试验中，需要具有 **编辑** 或 **管理** 权限。
* 若要列出和下载试验中的运行项目，需要具有 **读取** 权限。

有关详细信息，请参阅 [MLflow 项目权限](../../../security/access-control/workspace-acl.md#mlflow-artifact-permissions)。

## <a name="mlflow-usability-improvements"></a>MLflow 可用性改进

**2020 年 9 月 23-29 日：版本 3.29**

此版本包括以下 MLflow 可用性改进：

* MLflow“试验”和“注册的模型”页面现在包含帮助新用户入门的技巧。
* 现在，模型版本表显示模型版本的说明文本。 一个新列显示说明的前 32 个字符或第一行（以较短者为准）。

## <a name="new-azure-databricks-power-bi-connector-public-preview"></a>新的 Azure Databricks Power BI 连接器（公共预览版）

**2020 年 9 月 22 日**

Power BI Desktop 版本 2.85.681.0 包括一个更新的 Azure Databricks Power BI 连接器，它使 Azure Databricks 与 Power BI 之间的集成较之以前要无缝和可靠得多。 新连接器具有以下改进：

* 简单的连接配置：新的 Power BI Azure Databricks 连接器已集成到 Power BI 中，你只需要单击几下鼠标，即可使用简单的对话框对其进行配置。
* 使用 Azure Active Directory 身份验证的安全且无缝的身份验证。
* 由于新的 Azure Databricks ODBC 驱动程序提供了显著的性能改进，因此导入操作和优化的元数据调用操作速度更快。
* 通过 Power BI 访问 Azure Databricks 数据时，将遵守 Azure Databricks 表访问控制以及与你的 Azure AD 标识关联的 Azure 存储帐户权限。

有关详细信息，请参阅 [Power BI](../../../integrations/bi/power-bi.md)。

## <a name="use-customer-managed-keys-for-dbfs-root-public-preview"></a>为 DBFS 根使用客户管理的密钥（公共预览版）

**2020 年 9 月 15 日**

现在，你可以在 Azure Key Vault 中使用自己的加密密钥来加密 DBFS 存储帐户。 请参阅[为 DBFS 根配置客户管理的密钥](../../../security/keys/customer-managed-keys-dbfs/index.md)。

## <a name="mlflow-model-serving-public-preview"></a>MLflow 模型服务（公共预览版）

**2020 年 9 月 9-15 日：版本 3.28**

MLflow 模型服务目前以公共预览版提供。 使用 MLflow 模型服务，可以将在模型注册表中注册的 MLflow 模型部署为由 Azure Databricks 承载和管理的 REST API 终结点。 为已注册的模型启用模型服务时，Azure Databricks 将创建一个群集并部署该模型的所有非存档版本。

可以使用标准 Azure Databricks 身份验证通过 REST API 请求来查询所有模型版本。 模型访问权限从模型注册表继承而来，对已注册的模型拥有读取权限的任何人都可以查询任何已部署的模型版本。 当此服务为预览版时，建议将其用于低吞吐量和非关键应用程序。

有关详细信息，请参阅 [Azure Databricks 上的 MLflow 模型服务](../../../applications/mlflow/model-serving.md)。

## <a name="clusters-ui-improvements"></a>群集 UI 改进

**2020 年 9 月 9-15 日：版本 3.28**

[“群集”页面](../../../clusters/clusters-manage.md#display-clusters)现在针对“通用群集”和“作业群集”提供了单独的选项卡。  现在，每个选项卡上的列表已分页。 此外，我们还修复了有时候在创建群集后因为延迟而不能在 UI 中及时看到该群集的问题。

## <a name="visibility-controls-for-jobs-clusters-notebooks-and-other-workspace-objects"></a>针对作业、群集、笔记本和其他工作区对象的可见性控件

**2020 年 9 月 9-15 日：版本 3.28**

默认情况下，任何用户都可以查看 Azure Databricks UI 中显示的其工作区中的所有作业、群集、笔记本和文件夹，可以使用 Databricks API 列出它们，即使为这些对象启用了访问控制并且用户无权访问这些对象。

现在，任何 Azure Databricks 管理员都可以为笔记本和文件夹（工作区对象）、群集和作业启用可见性控制，以确保用户只能查看该管理员通过工作区、群集或作业访问控制为用户授予了访问权限的那些对象。

请参阅：

* [防止用户看到他们无权访问的工作区对象](../../../administration-guide/access-control/workspace-acl.md#workspace-object-visibility)
* [防止用户看到他们无权访问的群集](../../../administration-guide/access-control/cluster-acl.md#cluster-visibility)
* [防止用户看到他们无权访问的作业](../../../administration-guide/access-control/jobs-acl.md#jobs-visibility)

## <a name="ability-to-create-tokens-no-longer-permitted-by-default"></a>默认情况下不再允许使用令牌创建功能

**2020 年 9 月 9-15 日：版本 3.28**

对于在发布 Azure Databricks 平台 3.28 版之后创建的工作区，默认情况下，用户将不再能够生成个人访问令牌。 管理员必须显式授予这些权限，无论是向整个 `users` 组授予还是按用户或按组授予。 在 3.28 发布之前创建的工作区会保留已有的权限。

请参阅[管理个人访问令牌](../../../administration-guide/access-control/tokens.md)。

## <a name="mlflow-model-registry-supports-sharing-of-models-across-workspaces"></a>MLflow 模型注册表支持在工作区之间共享模型

**2020 年 9 月 9 日**

Azure Databricks 现在支持从多个工作区访问模型注册表。 你现在可以跨工作区注册模型、跟踪模型运行以及加载模型。 现在，多个团队可以共享对模型的访问权限，组织可以使用多个工作区来处理不同的开发阶段。 有关详细信息，请参阅[跨工作区共享模型](../../../applications/mlflow/multiple-workspaces.md)。

此功能需要 MLflow Python 客户端版本 1.11.0 或更高版本。

## <a name="databricks-runtime-73-beta"></a>Databricks Runtime 7.3（Beta 版本）

**2020 年 9 月 3 日**

Databricks Runtime 7.3、Databricks Runtime 7.3 ML 和用于基因组学的 Databricks Runtime 7.3 现已作为 Beta 版本发布。

有关信息，请参阅 [Databricks Runtime 7.3](../../runtime/7.3.md)、[Databricks Runtime 7.3 ML](../../runtime/7.3ml.md) 和[用于基因组学的 Databricks Runtime 7.3](../../runtime/7.3genomics.md) 发行说明。

## <a name="azure-databricks-workload-type-name-change"></a>Azure Databricks 工作负载类型名称变更

**2020 年 9 月 1 日**

群集使用的工作负荷类型的名称已更改：

* 数据工程 -> 作业计算
* 轻量数据工程 -> 作业轻量计算
* 数据分析 -> 通用计算

这些新名称将在发票和 EA 门户中与定价计划一起显示（例如，“高级 - 作业计算 - DBU”）。 有关详细信息，请参阅 [Azure Databricks 计量](https://azure.microsoft.com/updates/azure-analytics-data-bricks-meters/)。

在平台版本 3.27 中，用户界面也发生了更改（针对 8 月 25 日到 9 月 3 日之间的过渡版）：

在[“群集”页](../../../clusters/clusters-manage.md#display-clusters)上，列表标题已更改：

* 交互式群集 -> 通用群集
* 自动化群集 -> 作业群集

[为作业配置群集](../../../jobs.md#create-a-job)时，群集类型选项已更改：

* 新建自动化群集 -> 新建作业群集
* 现有交互式群集 -> 现有通用群集