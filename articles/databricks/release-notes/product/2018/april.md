---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 2018 年 4 月 - Azure Databricks
description: Azure Databricks 新增功能和改进功能的 2018 年 4 月发行说明。
ms.openlocfilehash: 6db0633182147f9f3aca2c29e5706f2600c725c2
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329442"
---
# <a name="april-2018"></a>2018 年 4 月

发布分阶段进行。 Azure Databricks 帐户可能要等到初始发布日期过后一周才会更新。

> [!NOTE]
>
> 现在，我们在 [Databricks Runtime 发行说明](../../runtime/index.md)中提供了 Databricks Runtime 弃用通知。

## <a name="secrets-cli"></a>机密 CLI

**2018 年 4 月 26 日**

Databricks CLI 版本 0.7.0 使你能够从命令行管理机密。 机密文档现在介绍如何使用机密 CLI 命令来创建和管理机密。

请参阅[机密管理](../../../security/secrets/index.md#secrets-user-guide)。

## <a name="deep-learning-guides"></a>深度学习指南

**2018 年 4 月 24 日**

我们添加了有关使用 CPU 群集在 Azure Databricks 上进行深度学习的文档。

请参阅[深度学习](../../../applications/machine-learning/train-model/deep-learning.md)。

## <a name="secrets-api-update-for-create-secret-scope"></a>用于创建机密范围的机密 API 更新

**2018 年 4 月 25 日至 5 月 1 日：版本 2.70**

“创建机密范围”终结点 (`2.0/preview/secret/scopes/create`) 现在弃用了字段 `initial_manage_acl`，改用 `initial_manage_principal`。 新字段提供了相同的功能，但语义更好。

请参阅[机密 API](../../../dev-tools/api/latest/secrets.md)。

## <a name="spark-error-tips"></a>Spark 错误提示

**2018 年 4 月 24 日至 5 月 1 日：版本 2.70**

Azure Databricks 现在提供提示，可帮助你解释和排查运行 Spark 命令时可能会遇到的许多错误。 我们会继续添加更多内容。

> [!div class="mx-imgBorder"]
> ![Spark 错误提示](../../../_static/images/notebooks/spark-error-tips.png)

## <a name="databricks-cli-070"></a>Databricks CLI 0.7.0

**2018 年 4 月 24 日**

Databricks CLI 0.7.0 包括 bug 修补程序。

它还为机密 API 提供了一个命令行界面。

请参阅 [Databricks CLI](../../../dev-tools/cli/index.md)。

## <a name="increase-init-script-output-truncation-limit"></a>提高初始化脚本输出截断限制

**2018 年 4 月 24 日至 5 月 1 日：版本 2.70**

我们已经将初始化脚本的输出截断限制增加到 500,000 个字符。

请参阅[群集节点初始化脚本](../../../clusters/init-scripts.md)。

## <a name="clusters-api-added-upsize_completed-event-type"></a>群集 API：添加了 UPSIZE_COMPLETED 事件类型

**2018 年 4 月 24 日至 5 月 1 日：版本 2.70**

新的 `UPSIZE_COMPLETED` 群集事件类型指示已将节点添加到群集。

请参阅群集 API 参考中的 [ClusterEventType](../../../dev-tools/api/latest/clusters.md#clustereventsclustereventtype)。

## <a name="command-autocomplete"></a>命令自动填写

**2018 年 4 月 10 日至 17 日：版本 2.69**

现在，Azure Databricks 在笔记本中支持两种类型的自动完成：本地自动完成和服务器自动完成。 本地自动完成会完成笔记本中存在的单词。 服务器自动完成的功能更强大，因为它会针对定义的类型、类和对象以及 SQL 数据库和表名称访问群集。 若要激活服务器自动完成功能，必须将笔记本附加到正在运行的群集并运行所有定义可完成对象的单元格。

> [!div class="mx-imgBorder"]
> ![笔记本自动完成](../../../_static/images/notebooks/notebook-autocomplete-object.png)

请参阅[自动完成](../../../notebooks/notebooks-use.md#autocomplete)。

## <a name="serverless-pools-upgraded-to-databricks-runtime-40"></a>无服务器池已升级到 Databricks Runtime 4.0

**2018 年 4 月 10 日**

无服务器池运行时版本已从 Databricks Runtime 3.5（包括 Apache Spark 2.2.1）升级到 [Databricks Runtime 4.0](../../runtime/4.0.md)（包括 Apache Spark 2.3.0）。 必须重启群集才能获取此更改。

升级代表 Apache Spark的次要版本更新，且后向兼容。

请参阅[高并发群集](../../../clusters/configure.md#high-concurrency)。