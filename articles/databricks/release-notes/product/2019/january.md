---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 2019 年 1 月 - Azure Databricks
description: 新 Azure Databricks 功能和改进的 2019 年 1 月发行说明。
ms.openlocfilehash: 89be3e1747468005e23397a4f2ddb108d4b7f290
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329436"
---
# <a name="january-2019"></a>2019 年 1 月

这些功能和 Azure Databricks 平台的改进已于 2019 年 1 月发布。

> [!NOTE]
>
> 发布分阶段进行。 在初始发布日期后，可能最长需要等待一周，你的 Azure Databricks 帐户才会更新。

## <a name="upcoming-change-python-3-to-become-the-default-when-you-create-clusters"></a>即将发生的更改：Python 3 将成为创建群集时的默认版本

**2019 年 1 月 29 日**

当 Databricks 平台版本 2.91 在二月中旬发布时，新群集的默认 Python 版本将从 Python 2 切换到 Python 3。 现有群集当然不会更改其 Python 版本。 但如果你习惯在新建群集时将 Python 2 作为默认版本，则需开始注意 Python 版本的选择。

> [!div class="mx-imgBorder"]
> ![默认 Python 版本](../../../_static/images/clusters/python-version-3.png)

## <a name="databricks-runtime-52-for-machine-learning-beta-release"></a>现已发布用于机器学习的 Databricks Runtime 5.2 (Beta)

**2019 年 1 月 24 日**

Databricks Runtime 5.2 ML 是基于 [Databricks Runtime 5.2（不受支持）](../../runtime/5.2.md)构建的。 它包含许多常见的机器学习库，包括 TensorFlow、PyTorch、Keras 和 XGBoost，并使用 Horovod 提供分布式 TensorFlow 训练。 除了自 Databricks Runtime ML 5.1 以来的库更新外，Databricks Runtime 5.2 ML 还包括以下新功能：

* 随着 Databricks 的性能优化，GraphFrames 现在支持 [Pregel API](https://graphframes.github.io/graphframes/docs/_site/api/scala/index.html#org.graphframes.lib.Pregel) ([Python](https://graphframes.github.io/graphframes/docs/_site/api/python/graphframes.html?highlight=pregel#graphframes.GraphFrame.pregel))。
* [HorovodRunner](../../../applications/machine-learning/train-model/distributed-training/horovod-runner.md#horovodrunner) 添加了以下功能：
  * 在 GPU 群集上，训练过程映射到 GPU 而不是工作器节点，以简化对多 GPU 实例类型的支持。 利用此内置支持，你可以分发到多 GPU 计算机上的所有 GPU 而无需使用自定义代码。
  * `HorovodRunner.run()` 现在返回来自第一个训练过程的返回值。

请参阅 [Databricks Runtime 5.2 ML（Beta 版本）](../../runtime/5.2ml.md)的完整发行说明。
d
## <a name="databricks-runtime-52-release"></a>现已发布 Databricks Runtime 5.2

**2019 年 1 月 24 日**

Databricks Runtime 5.2 现已推出。  Databricks Runtime 5.2 包括 Apache Spark 2.4.0、新增的 Delta Lake 和结构化流式处理功能与升级，以及已升级的 Python、R、Java 和 Scala 库。 有关详细信息，请参阅 [Databricks Runtime 5.2（不受支持）](../../runtime/5.2.md)。

## <a name="cluster-configuration-json-view"></a>群集配置 JSON 视图

**2019 年 1 月 15 日 - 22 日**

[群集配置](../../../clusters/configure.md#cluster-configurations)页现在支持 JSON 视图：

> [!div class="mx-imgBorder"]
> ![群集配置 JSON](../../../_static/images/clusters/cluster-json-azure.png)

JSON 视图为只读视图。 不过，你可以复制 JSON，并将它与[群集 API](../../../dev-tools/api/latest/clusters.md) 配合使用，以便创建和更新群集。

## <a name="cluster-ui"></a>群集 UI

**2019 年 1 月 15 日 -22 日：版本 2.89**

已清理“群集创建”页，并对其进行重新组织以方便使用，其中包括新的“高级选项”开关。

> [!div class="mx-imgBorder"]
> ![群集配置](../../../_static/images/release-notes/clusters-ui-azure.gif)

## <a name="deploy-azure-databricks-in-your-own-azure-virtual-network-vnet-injection"></a>在自己的 Azure 虚拟网络中部署 Azure Databricks（VNet 注入）

**2019 年 1 月 10 日**

> [!IMPORTANT]
>
> 此功能目前以[公共预览版](../../release-types.md)提供。

Azure Databricks 的默认部署是 Azure 上的完全托管服务：所有数据平面资源（包括与所有群集关联的[虚拟网络 (VNet)](/virtual-network/virtual-networks-overview)）都部署到锁定的资源组。 但如果需要进行网络自定义，那么现在可以在自己的虚拟网络中部署 Azure Databricks（有时称为 VNet 注入），以便能够：

* 使用[服务终结点](/virtual-network/virtual-network-service-endpoints-overview)以更安全的方式将 Azure Databricks 连接到其他 Azure 服务（如 Azure 存储）。
* 连接到[本地数据源](../../../administration-guide/cloud-configurations/azure/on-prem-network.md)以与 Azure Databricks 配合使用，从而利于[用户定义的路由](../../../administration-guide/cloud-configurations/azure/udr.md)。
* 将 Azure Databricks 连接到[网络虚拟设备](/architecture/reference-architectures/dmz/nva-ha)以检查所有出站流量并根据允许和拒绝规则执行操作。
* 将 Azure Databricks 配置为使用自定义 DNS。
* 配置[网络安全组 (NSG) 规则](/virtual-network/manage-network-security-group)以指定出口流量限制。
* 在现有虚拟网络中部署 Azure Databricks 群集。

通过将 Azure Databricks 部署到自己的虚拟网络中，还可以利用灵活的 CIDR 范围（虚拟网络的 CIDR 范围在 /16-/24 之间，子网的 CIDR 范围在 /18-/26 之间）。

使用 Azure 门户 UI 进行配置非常快捷：在创建工作区时，只需选择“在虚拟网络中部署 Azure Databricks 工作区”，然后选择虚拟网络，并提供两个子网的 CIDR 范围。 Azure Databricks 使用你提供的 CIDR 范围内的两个新的子网和网络安全组更新虚拟网络，将入站和出站子网流量列入允许列表，并将工作区部署到更新的虚拟网络。

> [!div class="mx-imgBorder"]
> ![工作区部署中的 VNet 注入](../../../_static/images/account-settings/vnet-inject-portal.gif)

如果希望自行配置用于 VNet 注入的虚拟网络（例如，你想要使用现有子网、使用现有网络安全组，或者创建自己的安全规则），则可以使用 [Azure Databricks 提供的 ARM 模板](../../../administration-guide/cloud-configurations/azure/vnet-inject.md#vnet-inject-advanced)（而不是门户 UI）。

> [!NOTE]
>
> 此功能以前仅通过注册提供。 它仍处于 **预览** 阶段，但现在完全是自助服务。

有关详细信息，请参阅[在 Azure 虚拟网络中部署 Azure Databricks（VNet 注入）](../../../administration-guide/cloud-configurations/azure/vnet-inject.md)和[将 Azure Databricks 工作区连接到本地网络](../../../administration-guide/cloud-configurations/azure/on-prem-network.md)。

## <a name="library-ui"></a>库 UI

**2019 年 1 月 2日 - 9 日：版本 2.88**

最初在 2018 年 11 月发布并随后很快还原的库 UI 改进功能已经重新发布。 这些更新使你可以更轻松地上传、安装和管理用于 Azure Databricks 群集的库。

Azure Databricks UI 现在支持工作区库和群集安装的库。 工作区库存在于工作区中，可安装到一个或多个群集上。 群集安装的库是只存在于其安装到的群集的上下文中的库。 此外：

* 现在可以从上传到对象存储的文件创建库。
* 现在可以从库详细信息页和群集的“库”选项卡中安装和卸载库。
* 现在，使用 API 安装的库会在群集的“库”选项卡中显示。

有关详细信息，请参阅[库](../../../libraries/index.md)。

## <a name="cluster-events"></a>群集事件

**2019 年 1 月 2日 - 9 日：版本 2.88**

已添加了新的群集事件以反映 Spark 驱动程序状态。 有关详细信息，请参阅 [ClusterEventType](../../../dev-tools/api/latest/clusters.md#clustereventsclustereventtype)。

## <a name="notebook-version-control-using-azure-devops-services"></a>使用 Azure DevOps Services 控制笔记本版本

**2019 年 1 月 2日 - 9 日：版本 2.88**

利用 Azure Databricks，现在可以轻松地使用 Azure DevOps Services（以前称为 VSTS）对笔记本进行版本控制。 身份验证是自动的，设置过程非常简单，并且你可以像通过 GitHub 集成管理笔记本修订版一样来管理笔记本修订版。

有关详细信息，请参阅 [Azure DevOps Services 版本控制](../../../notebooks/azure-devops-services-version-control.md)。