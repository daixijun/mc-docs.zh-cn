---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/01/2020
title: 2020 年 6 月 - Azure Databricks
description: Azure Databricks 新增功能和改进的 2020 年 6 月发行说明。
ms.openlocfilehash: 637ca94ce659749f0818c837fc03b053c1580564
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329159"
---
# <a name="june-2020"></a>2020 年 6 月

这些功能和 Azure Databricks 平台改进已于 2020 年 6 月发布。

> [!NOTE]
>
> 发布分阶段进行。 在初始发布日期后，可能最长需要等待一周，你的 Azure Databricks 帐户才会更新。

## <a name="databricks-connect-now-supports-databricks-runtime-66"></a>Databricks Connect 现在支持 Databricks Runtime 6.6

**2020 年 6 月 26 日**

[Databricks Connect](../../../dev-tools/databricks-connect.md) 现在支持 Databricks Runtime 6.6。

## <a name="databricks-runtime-70-ml-ga"></a>Databricks Runtime 7.0 ML 正式版

**2020 年 6 月 22 日**

Databricks Runtime 7.0 ML 基于 Databricks Runtime 7.0 构建，包含以下新功能：

* 笔记本范围内的 Python 库和自定义环境，通过 conda 和 pip 命令进行管理。
* 主要 Python 包的更新，包括 tensorflow、tensorboard、pytorch、xgboost、sparkdl 和 hyperopt。
* 新添加的 Python 包 lightgbm、nltk、petastorm 和 plotly。
* RStudio Server 开源版 v1.2。

有关详细信息，请参阅完整的 [Databricks Runtime 7.0 ML](../../runtime/7.0ml.md) 发行说明。

## <a name="databricks-runtime-70-ga-powered-by-apache-spark-30"></a>Databricks Runtime 7.0 正式版，由 Apache Spark 3.0 提供支持

**2020 年 6 月18 日**

Databricks Runtime 7.0 由 Apache Spark 3.0 提供支持，并且现在支持 [Scala 2.12](https://github.com/scala/scala/releases/tag/v2.12.11)。

Spark 3.0 引入了许多其他功能和改进，包括：

* 自适应查询执行，它是一种灵活的框架，可在 Spark SQL 中进行自适应执行，并支持在运行时更改减速器的数目。
* 重新设计的带类型提示的 pandas UDF。
* 结构化流式处理 Web UI。
* 与 ANSI SQL 标准更好的兼容性。
* 联接提示。

Databricks Runtime 7.0 添加了以下内容：

* 经过改进的[自动加载程序](../../../spark/latest/structured-streaming/auto-loader.md)，用于在 ETL 过程中新的数据文件到达云 Blob 存储时以增量方式处理这些文件。
* 经过改进的 [COPY INTO 命令](../../../spark/latest/spark-sql/language-manual/copy-into.md)，用于通过幂等重试将数据加载到 Delta Lake 中。
* 许多改进、库添加件和升级以及 bug 修复。

有关详细信息，请参阅完整的 [Databricks Runtime 7.0](../../runtime/7.0.md) 发行说明。

## <a name="databricks-runtime-70-for-genomics-ga"></a>用于基因组学的 Databricks Runtime 7.0 正式版

**2020 年 6 月18 日**

用于基因组学的 Databricks Runtime 7.0 是在 Databricks Runtime 7.0 基础上构建的，包含以下库更改：

* ADAM 库已从版本 0.30.0 更新到 0.32.0。
* Hail 库未包含在用于基因组学 Databricks Runtime 7.0 中，原因是没有基于 Apache Spark 3.0 的版本。

有关详细信息，请参阅[用于基因组学的 Databricks Runtime 7.0](../../runtime/7.0genomics.md) 的完整发行说明。

## <a name="stage-dependent-access-controls-for-mlflow-models"></a>用于 MLflow 模型的依赖于阶段的访问控制

**2020 年6月 16日 - 23 日：版本 3.22**

现在可以将与阶段相关的访问控制分配给用户或组，使他们能够在过渡或生产阶段管理注册到 [MLflow 模型注册表](../../../applications/mlflow/model-registry.md)中的 [MLflow 模型](../../../applications/mlflow/models.md)。 我们引入了两个新的权限级别：“可以管理过渡版本”和“可以管理生产版本”。 具有这些权限的用户可以在该级别允许的阶段之间执行转换。

有关详细信息，请参阅 [MLflow 模型权限](../../../security/access-control/workspace-acl.md#mlflow-model-permissions)。

## <a name="notebooks-now-support-disabling-auto-scroll"></a>Notebooks 现在支持禁用自动滚动

**2020 年6月 16日 - 23 日：版本 3.22**

使用 shift + enter [运行笔记本单元格](../../../notebooks/notebooks-use.md#run-a-cell)时，默认的笔记本行为是在单元格不可见时自动滚动到下一个单元格。 现在，可以在 ![“帐户”图标](../../../_static/images/account-settings/account-icon.png) >“用户设置”>“笔记本设置”中禁用自动滚动。 如果禁用自动滚动，则在使用 shift + enter 时焦点将移到下一个单元格，但笔记本不滚动到该单元格。

## <a name="metastore-ip-addresses-to-change-on-june-30-2020"></a>将于 2020 年 6 月 30 日更改元存储 IP 地址

**2020 年 6 月 11 日**

Azure Databricks 的默认元存储使用 Azure Database for MySQL。 用于 Azure Databricks 元存储的所有 Azure Database for MySQL IP 地址都会在 2020 年 6 月 30 日更改。 如果在自己的虚拟网络中部署了 Azure Databricks 工作区，则该部署的路由表可能包括一个 Azure Databricks 元存储 IP 地址，或者包括一个通往防火墙或代理设备的路由，此防火墙或代理设备的访问列表包含该地址。 如果是这样，则必须在 2020 年 6 月 30 日之前使用新的 MySQL IP 更新 Azure Databricks 路由表或防火墙，以避免中断。 有关详细信息，请参阅[使用新的 MySQL IP 更新 Azure Databricks 路由表和防火墙](/azure-databricks/mysql-ip-address)。

## <a name="internet-explorer-11-support-ends-on-august-15"></a>Internet Explorer 11 支持于 8 月 15 日结束

**2020 年 6 月 9 日**

为了跟上行业趋势，并确保客户拥有稳定且一致的用户体验，Azure Databricks 将在 2020 年 8 月 15 日结束对 Internet Explorer 11 的支持。

## <a name="databricks-runtime-62-series-support-ends"></a>Databricks Runtime 6.2 系列支持结束

**2020 年 6 月 3 日**

对 Databricks Runtime 6.2、用于机器学习的 Databricks Runtime 6.2 以及用于基因组学的 Databricks Runtime 6.2 的支持已于 6 月 3 日结束。 请参阅 [Databricks 运行时支持生命周期](../../runtime/databricks-runtime-ver.md#runtime-support)。

## <a name="simplify-and-control-cluster-creation-using-cluster-policies-public-preview"></a>使用群集策略简化和控制群集创建（公共预览版）

**2020 年 6 月 2 日 - 9 日：版本 3.21**

群集策略是管理员定义的可重复使用的群集模板，用于强制实施群集属性的规则，从而确保用户创建符合这些规则的群集。 作为 Azure Databricks 管理员，你现在可以创建群集策略并向用户授予策略权限。 这样，你就可以更好地控制创建的资源，为用户提供完成工作所需的灵活性级别，并大大简化群集创建体验。

有关详细信息，请参阅[管理群集策略](../../../administration-guide/clusters/policies.md)。

## <a name="scim-me-endpoint-now-returns-scim-compliant-response"></a>SCIM Me 终结点现在返回符合 SCIM 的响应

**2020 年 6 月 2 日 - 9 日：版本 3.21**

SCIM Me 终结点现在返回与 `/users/{id}` 终结点相同的信息，包括组和权利等信息。

请参阅 [SCIM API (Me)](../../../dev-tools/api/latest/scim/scim-me.md)。

## <a name="restrict-access-to-azure-databricks-using-ip-access-lists-public-preview"></a>使用 IP 访问列表限制对 Azure Databricks 的访问（公共预览版）

**2020 年 6 月 1 日**

现在可以配置 Azure Databricks 工作区，以使用户只能通过具有安全外围的现有企业网络连接到服务。 Azure Databricks 管理员可以使用 IP 访问列表 API 来定义一组已获批准的 IP 地址，包括允许列表和阻止列表。 对 Web 应用程序和 REST API 的所有传入访问均要求用户从经授权的 IP 地址进行连接，从而确保用户只有使用 VPN 才能从咖啡店或机场等公共网络访问工作区。

该功能需要 [Azure Databricks Premium 计划](https://databricks.com/product/azure-pricing)。

有关详细信息，请参阅 [IP 访问列表](../../../security/network/ip-access-list.md)。