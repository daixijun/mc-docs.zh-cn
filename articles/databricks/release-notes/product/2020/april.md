---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/24/2020
title: 2020 年 4 月 - Azure Databricks
description: 新 Azure Databricks 功能和改进的 2020 年 4 月发行说明。
ms.openlocfilehash: f9b8162b44fb03ce4abfed5cf8ad810871f161de
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329100"
---
# <a name="april-2020"></a>2020 年 4 月

这些功能和 Azure Databricks 平台的改进已于 2020 年 4 月发布。

> [!NOTE]
>
> 发布分阶段进行。 在初始发布日期后，可能最长需要等待一周，你的 Azure Databricks 帐户才会更新。

## <a name="unique-urls-for-each-azure-databricks-workspace"></a>每个 Azure Databricks 工作区的唯一 URL

**2020 年 4 月 28 日**

Azure Databricks 为每个工作区添加了新的唯一 URL。 这一新的每工作区 URL 采用以下格式：`adb-<workspaceId>.<random number>.databricks.azure.cn`。 此 URL 是对目前为止用于访问工作区的现有区域 URL (`<region>.databricks.azure.cn`) 的补充。 这两个 URL 都继续受到支持。 但是，我们强烈建议你使用新的每工作区 URL，因为它与区域无关。

如果你有使用旧区域 URL 的现有脚本或自动化工具，我们建议你更改脚本，使用新的每工作区 URL。 我们还建议你在以后的任何脚本或自动化工具中使用每工作区 URL。 但是，由于 Azure Databricks 仍将支持现有区域 URL，因此，在新的 URL 推出后，现有代码仍将有效。

有关详细信息，请参阅[从旧的区域 URL 迁移到每工作区 URL](../../../workspace/migrate-workspace-urls.md)。

## <a name="mlflow-tracking-ui-enhancement"></a>MLflow 跟踪 UI 增强功能

**2020 年 4 月 23 - 30 日：版本 3.18**

MLflow UI 现在提供了一种方法，用于在删除根运行时删除子运行。

## <a name="notebook-usability-improvements"></a>笔记本可用性提升

**2020 年 4 月 23 - 30 日：版本 3.18**

此版本在使用笔记本时引入了几个可用性方面的改进：

* 现在可以分别对上一个和下一个单元格使用 Shift + 向上箭头或向下箭头来选择相邻的笔记本单元格  。 可以复制、剪切、删除和粘贴多选的单元格。
* 删除单元格时，默认情况下会显示一个用于确认删除操作的对话框。 现在，可以在删除单元格时禁用确认对话框，方法是选择“不再显示此消息”复选框并单击“确认” 。 你还可以切换确认对话框，只需使用![“帐户”图标](../../../_static/images/account-settings/account-icon.png) >“用户设置”>“笔记本设置”中的“启用命令删除确认”选项即可 。

## <a name="use-azure-active-directory-tokens-to-authorize-to-the-databricks-rest-api-public-preview"></a>使用 Azure Active Directory 令牌向 Databricks REST API 进行授权（公共预览版）

**2020 年 4 月 23 - 30 日：版本 3.18**

现在，可以使用 Azure Active Directory 令牌向 Databricks [REST API 2.0](../../../dev-tools/api/latest/index.md) 授权。 使用 Azure Active Directory 令牌，可以对新工作区的创建和设置进行自动化。
服务主体是 Azure Active Directory 中的应用程序对象。 还可以使用 Azure Databricks 工作区中的服务主体自动执行工作流。

有关详细信息，请参阅[使用 Azure Active Directory 令牌进行身份验证](../../../dev-tools/api/latest/aad/index.md)。

## <a name="single-user-can-view-the-apache-spark-logs-on-a-passthrough-cluster"></a>单个用户可以查看直通群集上的 Apache Spark 日志

**2020 年 4 月 23 - 30 日：版本 3.18**

以前，如果用户不是管理员，标准模式群集上的凭据传递就不允许该用户查看 Apache Spark 日志。 现在，对于具有附加权限的用户，即使该用户不是管理员，也可以查看 Spark 日志。

## <a name="databricks-connect-now-supports-databricks-runtime-65"></a>Databricks Connect 现在支持 Databricks Runtime 6.5

**2020 年 4 月 20 日**

[Databricks Connect](../../../dev-tools/databricks-connect.md) 现在支持 Databricks Runtime 6.5。

## <a name="databricks-runtime-61-and-61-ml-support-ends"></a>Databricks Runtime 6.1 和 6.1 ML 支持结束

**2020 年 4 月 16 日**

对 Databricks Runtime 6.1 和用于机器学习的 Databricks Runtime 6.1 的支持已于 4 月 16 日结束。 请参阅 [Databricks 运行时支持生命周期](../../runtime/databricks-runtime-ver.md#runtime-support)。

## <a name="databricks-runtime-65-ga"></a>Databricks Runtime 6.5 正式版

**2020 年 4 月 14 日**

Databricks Runtime 6.5 引入了许多库升级和新功能，其中包括：

* Delta 表中所有写入、更新和删除操作的操作指标现在显示在表历史记录中
* 可以对在 Delta Lake 流式处理微批次中处理的数据进行速率限制
* Snowflake 连接器已更新为 2.5.9

有关详细信息，请参阅完整的 [Databricks Runtime 6.5](../../runtime/6.5.md) 发行说明。

## <a name="databricks-runtime-65-for-machine-learning-ga"></a>用于机器学习的 Databricks Runtime 6.5 正式版

**2020 年 4 月 14 日**

Databricks Runtime 6.5 ML 引入了以下库升级：

* MLflow 已从 1.5.0 升级到 1.7.0

有关详细信息，请参阅完整的 [Databricks Runtime 6.5 ML](../../runtime/6.5ml.md) 发行说明。

## <a name="databricks-runtime-65-for-genomics-ga"></a>用于基因组学的 Databricks Runtime 6.5 正式版

**2020 年 4 月 14 日**

用于基因组学的 Databricks Runtime 6.5 是基于 Databricks Runtime 6.5 构建的。 有关此版本中包含的改进和库的详细信息，请参阅完整的[用于基因组学的 Databricks Runtime 6.5](../../runtime/6.5genomics.md) 发行说明。

## <a name="databricks-runtime-60-and-60-ml-support-ends"></a>Databricks Runtime 6.0 和 6.0 ML 支持结束

**2020 年 4 月 1 日**

对 Databricks Runtime 6.0 和用于机器学习的 Databricks Runtime 6.0 的支持已于 4 月 1 日结束。 请参阅 [Databricks 运行时支持生命周期](../../runtime/databricks-runtime-ver.md#runtime-support)。