---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 06/03/2020
title: 2019 年 12 月 - Azure Databricks
description: 新 Azure Databricks 功能和改进的 2019 年 12 月发行说明。
ms.openlocfilehash: 7d957690b740530302d7ad4916e5514a230e61b6
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329229"
---
# <a name="december-2019"></a>2019 年 12 月

这些功能和 Azure Databricks 平台的改进已于 2019 年 12 月发布。

> [!NOTE]
>
> 发布分阶段进行。 Azure Databricks 帐户可能要等到初始发布日期后的一周或更长时间才会更新。

## <a name="databricks-connect-now-supports-databricks-runtime-62"></a>Databricks Connect 现在支持 Databricks Runtime 6.2

**2019 年 12 月 17 日**

[Databricks Connect](../../../dev-tools/databricks-connect.md) 现在支持 Databricks Runtime 6.2。

## <a name="configure-clusters-with-your-own-container-image-using-databricks-container-services-ga"></a>通过 Databricks 容器服务 (GA) 使用自己的容器映像配置群集

2019 年 12 月 16 日 **：版本 3.7**

Databricks 容器服务已在 Databricks Runtime 6.1 和 Azure Databricks 平台版本 3.7 中正式提供，通过该服务你可使用自己的容器映像来配置群集。 你可以将复杂环境预打包在容器中，将其发布到常用容器注册表，如 ACR、ECR 或 Docker Hub，然后让 Azure Databricks 拉取映像以创建群集。 一些示例用例包括：

* 库自定义 - 你可以完全控制你要安装的系统库
* 黄金容器环境 - 你的 Docker 映像是锁定的环境，永远不会更改
* Docker CI/CD 集成 - 可以将 Azure Databricks 与 Docker CI/CD 管道集成

还有许多其他用例，涵盖从“指定配置”到“安装机器学习包”等多种方案。

有关详细信息，请参阅[使用 Databricks 容器服务自定义容器](../../../clusters/custom-containers.md#containers)。

## <a name="databricks-runtime-62-for-genomics-ga"></a>用于基因组学的 Databricks Runtime 6.2 正式版

**2019 年 12 月 3 日**

用于基因组学的 Databricks Runtime 6.2 是基于 Databricks Runtime 6.2 构建的。 它包含用于基因组学的 Databricks Runtime 6.1 的许多改进和升级，包括：

* Firth 逻辑回归
* 用户定义的样本质量控制指标
* 管道转换器性能提升
* 更可靠的联合基因分型
* 简化了与 LOFTEE 的集成
* Hail 0.26.0
* Samtools 1.9

有关详细信息，请参阅完整的[用于基因组学的 Databricks Runtime 6.2（不受支持）](../../runtime/6.2genomics.md)发行说明。

## <a name="azure-databricks-scim-provisioning-connector-available-in-the-app-gallery"></a>应用库中提供的 Azure Databricks SCIM 预配连接器

**2019 年 12 月 3 日**

Azure Active Directory 应用库中已提供 [Azure Databricks SCIM 预配连接器](/active-directory/manage-apps/add-gallery-app)，现在可以更轻松地设置从 Azure Active Directory 到 Azure Databricks 的用户和组的预配。 有关详细信息，请参阅[为 Microsoft Azure Active Directory 配置 SCIM 预配](../../../administration-guide/users-groups/scim/aad.md)。

## <a name="databricks-runtime-53-and-54-support-ends"></a>Databricks Runtime 5.3 和 5.4 支持结束

**2019 年 12 月 3 日**

对 Databricks Runtime 5.3 和 5.4 的支持于 12 月 3 日结束。 请参阅 [Databricks 运行时支持生命周期](../../runtime/databricks-runtime-ver.md#runtime-support)。

## <a name="databricks-runtime-62-ml-ga"></a>Databricks Runtime 6.2 ML 正式版

**2019 年 12 月 3 日**

Databricks Runtime 6.2 ML 的正式发布引入了许多库升级，其中包括：

* TensorFlow 和 TensorBoard：1.14.0 到 1.15.0。
* PyTorch：1.2.0 到 1.3.0。
* tensorboardX：1.8 到 1.9。
* MLflow：1.3.0 到 1.4.0。
* Hyperopt：0.2-db1 与 Azure Databricks MLflow 集成。
* mleap-databricks-runtime 到 0.15.0，包括 mleap-xgboost-runtime。

有关详细信息，请参阅完整的 [Databricks Runtime 6.2 ML（不受支持）](../../runtime/6.2ml.md)发行说明。

## <a name="databricks-runtime-62-ga"></a>Databricks Runtime 6.2 正式版

**2019 年 12 月 3 日**

Databricks Runtime 6.2 的正式发布引入了新功能、改进和许多 bug 修复，包括：

* 优化的 Delta Lake 仅插入合并

有关详细信息，请参阅完整的 [Databricks Runtime 6.2（不受支持）](../../runtime/6.2.md)发行说明。

## <a name="databricks-connect-now-supports-databricks-runtime-61"></a>Databricks Connect 现在支持 Databricks Runtime 6.1

**2019 年 12 月 3 日**

[Databricks Connect](../../../dev-tools/databricks-connect.md) 现在支持 Databricks Runtime 6.1。 通过 Databricks Connect，可将喜欢的 IDE（IntelliJ、Eclipse、PyCharm、RStudio 和 Visual Studio）、笔记本服务器（Zeppelin 和 Jupyter）和其他自定义应用程序连接到 Azure Databricks 群集，并运行 Apache Spark 代码。