---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 2018 年 2 月 - Azure Databricks
description: 新 Azure Databricks 功能和改进的 2018 年 2 月发行说明。
ms.openlocfilehash: 03ffd03d896751bc4d6c0277349fb67a649dc242
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329240"
---
# <a name="february-2018"></a>2018 年 2 月

发布分阶段进行。 Azure Databricks 帐户可能要等到初始发布日期后的一周内才会更新。

## <a name="new-line-chart-supports-time-series-data"></a>新折线图支持时序数据

**2018 年 2 月 27 日 - 3 月 6 日：版本 2.66**

新的折线图完全支持时序数据，并通过我们的旧折线图选项解决限制问题。 旧的折线图已弃用，我们建议用户将使用旧折线图的所有可视化效果迁移到新的折线图。

![折线图](../../../_static/images/notebooks/line-chart.png)

有关详细信息，请参阅[旧版折线图](../../../notebooks/visualizations/legacy-charts.md)。

## <a name="more-visualization-improvements"></a>更多的可视化效果改进

**2018 年 2 月 27 日 - 3 月 6 日：版本 2.66**

现在可以对表输出中的列进行排序，并在图表中使用 10 个以上的图例项。

## <a name="delete-job-runs-using-job-api"></a>使用作业 API 删除作业运行

**2018 年 2 月 27 日 - 3 月 6 日：版本 2.66**

你现在可以使用新的 `jobs/runs/delete` 终结点通过作业 API 来删除作业运行。

有关详细信息，请参阅[运行删除](../../../dev-tools/api/latest/jobs.md#jobsjobsservicedeleterun)。

## <a name="katex-math-rendering-library-updated"></a>已更新 KaTeX 数学呈现库

**2018 年 2 月 27 日 - 3 月 6 日：版本 2.66**

Azure Databricks 用于数学公式呈现的 KatTeX 版本已从 0.5.1 更新为 0.9.0-beta1。

此更新引入了一些更改，可能会更改 0.5.1 中编写的表达式：

* `\xLongequal` 现在为 `\xlongequal` (#997)
* `[text]color` HTML 颜色必须格式标准。 (#827)
* `\llap` 和 `\rlap` 现在在数学模式下呈现内容。 使用 `\mathllap`（新）和 `\mathrlap`（新）提供以前的行为。
* 现在 `\color` 和 `\textcolor` 的行为与它们在 LaTeX (#619) 中的行为相同

有关详细信息，请参阅 [KaTeX 发行说明](https://github.com/Khan/KaTeX/releases)。

## <a name="databricks-cli-050-release"></a>Databricks CLI：版本 0.5.0

**2018 年 2 月 27 日：databricks-cli 0.5.0**

Databricks CLI 现在支持面向[库 API](../../../dev-tools/api/latest/libraries.md) 的命令。

CLI 现在还支持多个连接配置文件。 可以使用连接配置文件将 CLI 配置为与多个 Azure Databricks 部署通信。

有关详细信息，请参阅 [Databricks CLI](../../../dev-tools/cli/index.md)。

## <a name="dbutils-api-library"></a>DBUtils API 库

**2018 年 2 月 13 日至 20 日：版本 2.65**

Azure Databricks 提供了各种实用工具 API，使你能够轻松地使用 DBFS、笔记本工作流和小组件。 `dbutils-api` 库可加速应用程序开发，方法是：允许在本地编译和运行针对这些实用工具 API 的单元测试，然后将应用程序部署到 Azure Databricks 群集。

有关详细信息，请参阅 [Databricks 实用工具 API 库](../../../dev-tools/databricks-utils.md#dbutils-api)。

## <a name="filter-for-your-jobs-only"></a>仅筛选作业

**2018 年 2 月 13 日至 20 日：版本 2.65**

借助“作业”列表中的新筛选器，可仅显示你拥有的作业和你有权访问的作业。

![作业筛选器](../../../_static/images/jobs/jobs-filter-my.png)

有关详细信息，请参阅[作业](../../../jobs.md)。

## <a name="spark-submit-from-the-create-job-page"></a>从“创建作业”页进行 Spark 提交

**2018 年 2 月 13 日至 20 日：版本 2.65**

现在，你可以从“创建作业”页，以及通过 REST API 或 CLI 配置 `spark-submit` 参数。

![Spark-submit](../../../_static/images/jobs/jobs-spark-submit-params.png)

有关详细信息，请参阅[作业](../../../jobs.md)。

## <a name="select-python-3-from-the-create-cluster-page"></a>从“创建群集”页选择 Python 3

**2018 年 2 月 13 日至 20 日：版本 2.65**

现在，你可以在创建群集时，在新的 Python 版本下拉菜单中指定 Python 版本 2 或版本 3。 如果未进行选择，则 Python 2 为默认值。 你还可以像以前一样，使用 REST API 创建 Python 3 群集。

![Python 版本](../../../_static/images/clusters/python-select.png)

有关详细信息，请参阅 [Python 版本](../../../clusters/configure.md#python-3)。

## <a name="workspace-ui-improvements"></a>工作区 UI 改进

**2018 年 2 月 13 日至 20 日：版本 2.65**

我们已在工作区文件浏览器中添加了按类型（文件夹、笔记本、库）对文件进行排序的功能，并且主文件夹始终显示在“用户”列表的顶部。

![工作区排序](../../../_static/images/workspace/workspace-sort.png)

## <a name="autocomplete-for-sql-commands-and-database-names"></a>自动填写 SQL 命令和数据库名称

**2018 年 2 月 13 日至 20 日：版本 2.65**

笔记本中的 SQL 单元现在提供 SQL 命令和数据库名称的自动完成功能。

## <a name="serverless-pools-now-support-r"></a>无服务器池现在支持 R

**2018 年 2 月 1 日至 8 日：版本 2.64**

你现在可以在[无服务器池](../../../clusters/configure.md#high-concurrency)中使用 R。

## <a name="xgboost-available-as-a-spark-package"></a>可用作 Spark 包的 XGBoost

**2018 年 2 月 1 日至 8 日：版本 2.64**

XGBoost 的 Spark 集成库现在可以作为 Spark 包从库 UI 或 REST API 安装到 Azure Databricks。 以前，XGBoost 需要通过 init 脚本从源进行安装，因而群集启动时间更长。 有关详细信息，请参阅 [在 Azure Databricks 上安装 XGBoost](../../../applications/machine-learning/train-model/install-xgboost.md)。

## <a name="table-access-control-for-sql-and-python-beta"></a>SQL 和 Python 的表访问控制 (Beta)

**2018 年 2 月 1 日至 8 日：版本 2.64**

去年，我们为 SQL 用户引入了数据对象访问控制。 今天，我们很高兴地宣布为 SQL 和 Python 用户提供表访问控制（表 ACL）的公共 beta 版本。 借助表访问控制，可以限制对安全对象（如表、数据库、视图或函数）的访问权限。 还可以对包含任意查询的派生视图设置权限来提供细粒度的访问控制（例如，针对符合特定条件的行和列）。

> [!NOTE]
>
> * 此功能目前以公共 beta 版本提供
> * 此功能需要 Databricks Runtime 3.5+。

有关详细信息，请参阅[数据对象特权](../../../security/access-control/table-acls/object-privileges.md)。