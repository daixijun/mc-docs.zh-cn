---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/23/2020
title: 2020 年 8 月 - Azure Databricks
description: 新 Azure Databricks 功能和改进的 2020 年 8 月发行说明。
ms.openlocfilehash: 39378b511f51521d3b3546854ee7ac33a29e1484
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94328989"
---
# <a name="august-2020"></a>2020 年 8 月

这些功能和 Azure Databricks 平台的改进已于 2020 年 8 月发布。

> [!NOTE]
>
> 发布分阶段进行。 在初始发布日期后，可能最长需要等待一周，你的 Azure Databricks 帐户才会更新。

> [!IMPORTANT]
>
> 仅面向加拿大中部和印度中部区域的客户发布了版本 3.26。 在发布 3.27 的同时，所有其他区域都将获得 3.26 版本的功能。

## <a name="token-management-api-is-ga-and-admins-can-use-the-admin-console-to-grant-and-revoke-user-access-to-tokens"></a>令牌管理 API 已正式发布，管理员可使用管理控制台向用户授予和撤销对令牌的访问权限i

2020 年 8 月 26 日 - 9 月 1 日：版本 3.27

令牌管理现已正式发布。 Azure Databricks 管理员可以使用令牌管理 API 和管理员控制台来管理其用户的 Azure Databricks 个人访问令牌。 作为管理员，你可以：

* 监视和撤销用户的个人访问令牌。
* 控制工作区中未来令牌的生存期。
* 通过权限 API 或管理员控制台来控制哪些用户可以创建和使用令牌。

在公共预览版向正式发布版转换的期间，令牌管理 API 参数 `created_by` 更改为 `created_by_id`，并添加了一个新参数 `created_by_username`。

有关详细信息，请参阅[管理个人访问令牌](../../../administration-guide/access-control/tokens.md)。

## <a name="message-size-limits-for-shiny-apps-increased"></a>Shiny 应用的消息大小已增加

2020 年 8 月 26 日 - 9 月 1 日：版本 3.27

Shiny 应用的最大应用程序大小已从 10 MB 增加到 20 MB。 如果应用程序的总大小超过此限制，请参阅 [Shiny FAQ](../../../spark/latest/sparkr/shiny.md#frequently-asked-questions-faq) 获取相关建议。

## <a name="improved-instructions-for-setting-up-a-cluster-in-local-mode"></a>改进了有关在本地模式设置群集的说明

2020 年 8 月 26 日 - 9 月 1 日：版本 3.27

在群集 UI 中：

* 如果创建的群集不具有任何辅助角色，会出现一个工具提示，建议使用本地模式并显示关联的配置设置 (`spark.master local[*]`)。
* 无法再为群集设置 `spark.master local[*]`，除非该群集不具有任何辅助角色。

## <a name="view-version-of-notebook-associated-with-a-run"></a>查看与运行关联的笔记本版本

2020 年 8 月 26 日 - 9 月 1 日：版本 3.27

现在可以从“试验”边栏中显示与运行相关联的笔记本版本。 有关详细信息，请参阅[查看笔记本试验](../../../applications/mlflow/tracking.md#view-notebook-experiment)。

## <a name="databricks-runtime-72-ga"></a>Databricks Runtime 7.2 正式版

2020 年 8 月 20 日

在 Databricks Runtime 7.1 的基础上，Databricks Runtime 7.2 引入了许多额外的功能和改进，包括：

* [自动加载程序](../../../spark/latest/structured-streaming/auto-loader.md)已正式发布：自动加载程序是一种有效的方法，可将大量文件以增量方式引入 Delta Lake。 它现已正式发布，并添加了以下功能：
  * 目录列表模式选项：除了现有的文件通知模式外，自动加载程序还添加了新的目录列表模式，用于确定何时有新文件。
  * 云资源管理 API：现在你可以使用我们的 Scala API 来管理由自动加载程序创建的云资源。 你可以使用此 API 列出通知服务并删除特定的通知服务。
  * 速率限制选项：现在你可以使用 `cloudFiles.maxBytesPerTrigger` 选项来限制每个微批中处理的数据量。
  * 选项验证：自动加载程序现在会验证你提供的选项。`validation` 将失败。 若要跳过选项验证，请将 `cloudFiles.validateOptions` 设置为 `false`。
* [通过克隆高效复制 Delta 表](../../../delta/delta-utility.md#clone-delta-table)。
* 改进：
  * Snowflake 连接器已升级到版本 2.8.1，其中包括 Spark 3.0 支持。
  * 凭据传递身份验证改进
  * TensorBoard 改进
  * 升级了 Python 和 R 库

有关详细信息，请参阅完整的 [Databricks Runtime 7.2](../../runtime/7.2.md) 发行说明。

## <a name="databricks-runtime-72-ml-ga"></a>Databricks Runtime 7.2 ML 正式版

2020 年 8 月 20 日

用于机器学习的 Databricks Runtime 7.2 基于 Databricks Runtime 7.2 构建，并引入了已改进的全新 Python 和系统库。 有关详细信息，请参阅完整的 [Databricks Runtime 7.2 ML](../../runtime/7.2ml.md) 发行说明。

## <a name="databricks-runtime-72-genomics-ga"></a>Databricks Runtime 7.2 Genomics 正式版

2020 年 8 月 20 日

用于基因组学的 Databricks Runtime 7.2 基于 Databricks Runtime 7.2 构建，并极大地加快了将文本 numpy 1D 和 2D 浮动类型的 n 维数组转换为 Java 数组的速度。 Glow [基因组范围的关联研究文档](https://glow.readthedocs.io/en/latest/tertiary/regression-tests.html)反映使用情况。

有关详细信息，请参阅完整的[用于基因组学的 Databricks Runtime 7.2](../../runtime/7.2genomics.md) 发行说明。

## <a name="permissions-api-public-preview"></a>权限 API（公共预览版）

**2020 年 8 月 18 日**

Databricks 很高兴地宣布推出权限 API 的公共预览版，你可以使用它来管理以下内容的权限：

* 令牌
* 群集
* 池
* 作业
* 笔记本
* 文件夹（目录）
* MLflow 注册模型

有关详细信息，请参阅[权限 API](../../../dev-tools/api/latest/permissions.md)。

## <a name="databricks-connect-71-ga"></a>Databricks Connect 7.1 (GA)

**2020 年 8 月 12 日**

[Databricks Connect](../../../dev-tools/databricks-connect.md) 现在支持 Databricks Runtime 7.1。

在 Databricks Runtime 7.1 中，Databricks 建议始终使用 Databricks Connect 的最新版本。

## <a name="repeatable-installation-order-for-cluster-libraries"></a>群集库的可重复安装顺序

2020 年 8 月 12 日至 25 日：版本 3.26

在运行 Databricks Runtime 7.2 或更高版本的群集上，Azure Databricks 现在按照安装所有群集库的顺序对其进行处理。

## <a name="create-model-from-mlflow-registered-models-page-public-preview"></a>从 MLflow 注册模型页创建模型（公共预览版）

2020 年 8 月 12 日至 25 日：版本 3.26

现在可以从 MLflow 注册模型页中创建新模型。 有关详细信息，请参阅[在模型注册表中注册模型](../../../applications/mlflow/model-registry.md#register-a-model-in-the-model-registry)。

## <a name="databricks-container-services-supports-gpu-images"></a>Databricks 容器服务支持 GPU 图像

2020 年 8 月 12 日至 25 日：版本 3.26

现在可以在具有 GPU 的群集上使用 [Databricks 容器服务](../../../clusters/custom-containers.md)来通过自定义库创建可移植的深度学习环境。

有关详细信息，请参阅 [GPU 群集上的 Databricks 容器服务](../../../clusters/gpu.md#databricks-container-services-on-gpu-clusters)。