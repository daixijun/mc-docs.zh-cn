---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/26/2020
title: 2020 年 2 月 - Azure Databricks
description: Azure Databricks 新功能和改进的 2020 年 2 月发行说明。
ms.openlocfilehash: 2a6f17083f12731e4a1e9f10c682e9acffaf887a
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329090"
---
# <a name="february-2020"></a>2020 年 2 月

这些功能和 Azure Databricks 平台改进已于 2020 年 2 月发布。

> [!NOTE]
>
> 发布分阶段进行。 在初始发布日期后，可能最长需要等待一周，你的 Azure Databricks 帐户才会更新。

## <a name="databricks-runtime-64-for-genomics-ga"></a>用于基因组学的 Databricks Runtime 6.4 正式版

**2020 年 2 月 26 日**

用于基因组学的 Databricks Runtime 6.4 是在 Databricks Runtime 6.4 基础上构建的。 它包含用于基因组学的 Databricks Runtime 6.3 的许多改进和升级。

关键功能包括：

* 现可自定义 [DNASeq](../../../applications/genomics/secondary/dnaseq-pipeline.md)。 管道用户可选择性地禁用读取比对、变体识别和变体批注阶段的任意合法组合。 用户还可执行单端读取比对。
* 用于基因组学的 Databricks Runtime 6.4 中包含的 [Glow](https://projectglow.io) 版本现在为以前仅通过 SQL 表达式公开的函数提供了 Python 和 Scala API。 这些函数可用于 DataFrame 操作，从而提高了编译时安全性。

有关详细信息，请参阅[用于基因组学的 Databricks Runtime 6.4](../../runtime/6.4genomics.md) 的完整发行说明。

## <a name="databricks-runtime-64-ml-ga"></a>Databricks Runtime 6.4 ML 正式版

**2020 年 2 月 26 日**

Databricks Runtime 6.4 ML 正式版引入了库升级，其中包括：

* PyTorch：1.3.1 到 1.4.0
* Horovod：0.18.2 到 1.19.0

有关详细信息，请参阅完整的 [Databricks Runtime 6.4 ML](../../runtime/6.4ml.md) 发行说明。

## <a name="databricks-runtime-64-ga"></a>Databricks Runtime 6.4 正式版

**2020 年 2 月 26 日**

Databricks Runtime 6.4 正式版引入了新功能、改进和许多 bug 修补程序。

* 用自动加载程序（公共预览版）增量处理新的数据文件。 当新的数据文件在 ETL 过程中到达云 Blob 存储时，你可使用自动加载程序更高效地以增量方式处理这些文件。 这是对基于文件的结构化流的改进，它通过列出云目录和跟踪已查看的文件来识别新文件，随着目录的扩大，它的效率可能会非常低。
* 将数据加载到具有幂等重试的 Delta Lake（公共预览版）。 通过 SQL 命令 `COPY INTO`，可将数据加载到具有幂等重试的 Delta Lake（公共预览版）。 若要将数据加载到 Delta Lake，现在必须使用 Apache Spark DataFrame API。 如果在加载过程中出现故障，必须有效地处理它们。
* 对 Delta 表的所有写入、更新和删除操作的操作指标现显示在表历史记录中。
* Azure Databricks 笔记本（公共预览版）中默认启用内联 Matplotlib 图。

有关详细信息，请参阅完整的 [Databricks Runtime 6.4](../../runtime/6.4.md) 发行说明。

## <a name="new-interactive-charts-offer-rich-client-side-interactions"></a>新的交互式图表提供丰富的客户端交互

**2019 年 2 月 25 日 - 3 月 3 日：版本 3.14**

此版本引入了两种新的交互式图表类型，它们会取代条形图和折线图实现形式。 除了现有的图表功能以外，折线图还具有几个新的自定义绘图选项：设置 Y 轴范围、显示/隐藏标记，以及将日志比例应用到 Y 轴。 两种图表都具有内置工具栏，后者支持一组丰富的客户端交互。

> [!div class="mx-imgBorder"]
> ![图表工具栏](../../../_static/images/notebooks/chart-toolbar.png)

如果要使用现有的图表实现形式，可从“旧版图表”下拉菜单中选择它们。 现有图表将继续使用之前可用的实现形式。

> [!div class="mx-imgBorder"]
> ![旧版图表类型](../../../_static/images/notebooks/display-legacy-charts.png)

## <a name="new-data-ingestion-network-adds-partner-integrations-with-delta-lake-public-preview"></a>新的数据引入网络添加了与 Delta Lake 的合作伙伴集成（公共预览版）

**2020 年 2 月 24 日**

现在，你可轻松地将你的“lakehouse”从数百个数据源填充到 Delta Lake；其中 lakehouse 是你的数据库，依托于你通常借助数据仓库获取的各种数据结构和数据管理功能。 此网络的核心是新的合作伙伴集成库，可从你的工作区进行访问，还可借助它通过我们的合作伙伴 Fivetran、Qlik、Infoworks、Streamsets 和 Syncsort 访问大型数据源网络。

> [!div class="mx-imgBorder"]
> ![合作伙伴集成门户](../../../_static/images/release-notes/partner-integrations.png)

有关概述，请参阅我们的[博客](https://databricks.com/blog/2020/02/24/new-databricks-data-ingestion-network-for-applications-database-and-big-data-integrations-into-delta-lake.html)。 有关详细信息，请参阅[合作伙伴数据集成](../../../integrations/ingestion/index.md)。

## <a name="workspace-creator-automatically-added-as-an-azure-databricks-admin"></a>工作区创建者自动添加为 Azure Databricks 管理员

**2020 年 2 月 24 日**

在 2020 年 2 月 24 日之前，仅当创建了 Azure Databricks 工作区的用户还在 Azure 门户中单击了“启动工作区”按钮时，该用户才被添加为该工作区的管理员用户，或者该用户由工作区中已经是管理员用户的用户添加为管理员（订阅中任何单击了“启动工作区”按钮的 Azure 参与者都会被创建为该工作区中的管理员用户） 。 而现在，创建工作区的用户将被自动添加为工作区管理员。

若要详细了解如何创建和启动工作区，请参阅[管理订阅](../../../administration-guide/account-settings/account.md)

## <a name="flags-to-manage-workspace-security-and-notebook-features-now-available"></a>用于管理工作区安全性和笔记本功能的标志现已可用

**2020 年 2 月 4 日至 11 日：版本 3.12**

此版本引入了新的标记，它们用于管理发送来阻止攻击工作区的安全性标头，以及笔记本结果下载和 Git 版本控制的访问权限。 请参阅[管理工作区安全性标头](../../../administration-guide/workspace/security.md)和[管理对笔记本功能的访问权限](../../../administration-guide/workspace/notebooks.md)。 所有这些管理选项均默认启用。