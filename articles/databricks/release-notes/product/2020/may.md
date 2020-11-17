---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 07/22/2020
title: 2020 年 5 月 - Azure Databricks
description: Azure Databricks 新功能和改进的 2020 年 5 月发行说明。
ms.openlocfilehash: 852ac06ce60e613f9b5f4dc5758dd86525ba84d1
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329209"
---
# <a name="may-2020"></a>2020 年 5 月

这些功能和 Azure Databricks 平台改进已于 2020 年 5 月发布。

> [!NOTE]
>
> 发布分阶段进行。 在初始发布日期后，可能最长需要等待一周，你的 Azure Databricks 帐户才会更新。

## <a name="easv4-series-vms-beta"></a>Easv4 系列 VM（Beta 版本）

**2020 年 5 月 29 日**

Azure Databricks 现在为 [Easv4 系列](/virtual-machines/eav4-easv4-series) 虚拟机 (VM) 提供 Beta 版本支持，这些 VM 使用高级 SSD 并可将最大频率提高为 3.35 GHz。 这些实例类型可优化内存密集型企业应用程序的工作负载性能。

## <a name="databricks-runtime-66-for-genomics-ga"></a>用于基因组学的 Databricks Runtime 6.6 正式版

**2020 年 5 月 26 日**

用于基因组学的 Databricks Runtime 6.6 是在 Databricks Runtime 6.6 基础上构建的，包含以下新功能：

* GFF3 读取器
* 自定义引用基因组支持
* 每样本管道超时
* BAM 导出选项
* 清单 Blob

有关详细信息，请参阅[用于基因组学的 Databricks Runtime 6.6](../../runtime/6.6genomics.md) 的完整发行说明。

## <a name="databricks-runtime-66-ml-ga"></a>Databricks Runtime 6.6 ML 正式版

**2020 年 5 月 26 日**

Databricks Runtime 6.6 ML 基于 Databricks Runtime 6.6 构建，包含以下新功能：

* mlflow 已升级：1.7.0 到 1.8.0

有关详细信息，请参阅完整的 [Databricks Runtime 6.6 ML](../../runtime/6.6ml.md) 发行说明。

## <a name="databricks-runtime-66-ga"></a>Databricks Runtime 6.6 正式版

**2020 年 5 月 26 日**

Databricks Runtime 6.6 引入了许多库升级和新功能，其中包括以下 Delta Lake 功能：

* 现可通过 `merge` 操作自动提升表的架构。 如果你想要将更改数据更新插入到一个表中，而且数据架构会随时间推移而变化，那么此功能非常有用。
  `merge` 可同时改进架构和更新插入更改，而不是在更新插入之前检测和应用架构更改。 请参阅[自动架构演变](../../../delta/delta-update.md#merge-schema-evolution)。
* 仅包含匹配子句的合并操作（即仅有 `update` 和 `delete` 操作，没有 `insert` 操作）的性能已得到改进。
* Hive 元存储中引用的 Parquet 表现可使用 `CONVERT TO DELTA` 通过其表识别符转换为 Delta Lake。

有关详细信息，请参阅完整的 [Databricks Runtime 6.6](../../runtime/6.6.md) 发行说明。

## <a name="dbfs-rest-api-delete-endpoint-size-limit"></a>DBFS REST API 删除终结点大小限制

**2020 年 5 月 21 日至 28 日：版本 3.20**

使用 [DBFS API](../../../dev-tools/api/latest/dbfs.md) 以递归方式删除大量文件时，删除操作以增量方式执行。 此调用在大约 45 秒后返回响应，并出现一条错误消息，要求你重新调用删除操作，直至完全删除目录结构。 例如：

```json
{
  "error_code":"PARTIAL_DELETE","message":"The requested operation has deleted 324 files. There are more files remaining. You must make another request to delete more."
}
```

## <a name="easily-view-large-numbers-of-mlflow-registered-models"></a>轻松查看大量已注册的 MLflow 模型

**2020 年 5 月 21 日至 28 日：版本 3.20**

MLflow 模型注册表现支持对已注册模型进行服务器端搜索和分页，这使具有大量模型的组织可高效地执行列出和搜索操作。 与之前一样，可按名称搜索模型并获取按名称或上次更新时间排序的结果。 但是，如果你有大量模型，这些页面的加载速度要快得多，搜索将提取最新的模型视图。

## <a name="libraries-configured-to-be-installed-on-all-clusters-are-not-installed-on-clusters-running-databricks-runtime-70-and-above"></a>配置为要安装在所有群集上的库未安装在运行 Databricks Runtime 7.0 及更高版本的群集上

**2020 年 5 月 21 日至 28 日：版本 3.20**

在 Databricks Runtime 7.0 及更高版本中，Apache Spark 的基础版本使用 Scala 2.12。 针对 Scala 2.11 编译的库可能会以意外的方式禁用 Databricks Runtime 7.0 群集，因此运行 Databricks Runtime 7.0 及更高版本的群集不会安装[配置为在所有群集上安装](../../../libraries/cluster-libraries.md#install-workspace-libraries)的库。 群集的[“库”选项卡](../../../libraries/cluster-libraries.md#view-the-libraries-installed-on-a-cluster)会显示状态 `Skipped`，以及与库处理中的更改相关的弃用消息。

如果你的群集是在早期版本的 Databricks Runtime（在 3.20 版发布到工作区之前的版本）上创建的，而且你现在将该群集编辑为使用 Databricks Runtime 7.0，那么配置为在所有群集上安装的任何库都将安装在该群集上。 在这种情况下，已安装的库中的所有不兼容的 JAR 都可能导致群集被禁用。 解决方法是克隆群集或创建新群集。

## <a name="databricks-runtime-70-for-genomics-beta"></a>用于基因组学的 Databricks Runtime 7.0（Beta 版本）

**2020 年 5 月 21 日**

用于基因组学的 Databricks Runtime 7.0 是在 Databricks Runtime 7.0 基础上构建的，包含以下库更改：

* ADAM 库已从版本 0.30.0 更新为 0.32.0.
* Hail 库未包含在用于基因组学 Databricks Runtime 7.0 中，原因是没有基于 Apache Spark 3.0 的版本。

有关详细信息，请参阅[用于基因组学的 Databricks Runtime 7.0](../../runtime/7.0genomics.md) 的完整发行说明。

## <a name="databricks-runtime-70-ml-beta"></a>Databricks Runtime 7.0 ML（Beta 版本）

**2020 年 5 月 21 日**

Databricks Runtime 7.0 ML 基于 Databricks Runtime 7.0 构建，包含以下新功能：

* 笔记本范围的 Python 库和自定义环境，由 conda 和 pip 命令进行管理。
* 主要 Python 包的更新，包括 tensorflow、tensorboard、pytorch、xgboost、sparkdl 和 hyperopt。
* 新添加的 Python 包 lightgbm、nltk、petastorm 和 plotly。
* RStudio Server 开源版 v1.2。

有关详细信息，请参阅完整的 [Databricks Runtime 7.0 ML](../../runtime/7.0ml.md) 发行说明。

## <a name="databricks-runtime-66-for-genomics-beta"></a>用于基因组学的 Databricks Runtime 6.6（Beta 版本）

**2020 年 5 月 7 日**

用于基因组学的 Databricks Runtime 6.6 是在 Databricks Runtime 6.6 基础上构建的，包含以下新功能：

* GFF3 读取器
* 自定义引用基因组支持
* 每样本管道超时
* BAM 导出选项
* 清单 Blob

有关详细信息，请参阅[用于基因组学的 Databricks Runtime 6.6](../../runtime/6.6genomics.md) 的完整发行说明。

## <a name="databricks-runtime-66-ml-beta"></a>Databricks Runtime 6.6 ML（Beta 版本）

**2020 年 5 月 7 日**

Databricks Runtime 6.6 ML 基于 Databricks Runtime 6.6 构建，包含以下新功能：

* mlflow 已升级：1.7.0 到 1.8.0

有关详细信息，请参阅完整的 [Databricks Runtime 6.6 ML](../../runtime/6.6ml.md) 发行说明。

## <a name="databricks-runtime-66-beta"></a>Databricks Runtime 6.6（Beta 版本）

**2020 年 5 月 7 日**

Databricks Runtime 6.6（Beta 版本）引入了许多库升级和新功能，其中包括以下 Delta Lake 功能：

* 现可通过 `merge` 操作自动提升表的架构。 如果你想要将更改数据更新插入到一个表中，而且数据架构会随时间推移而变化，那么此功能非常有用。
  `merge` 可同时改进架构和更新插入更改，而不是在更新插入之前检测和应用架构更改。 请参阅[自动架构演变](../../../delta/delta-update.md#merge-schema-evolution)。
* 仅包含匹配子句的合并操作（即仅有 `update` 和 `delete` 操作，没有 `insert` 操作）的性能已得到改进。
* Hive 元存储中引用的 Parquet 表现可使用 `CONVERT TO DELTA` 通过其表识别符转换为 Delta Lake。

有关详细信息，请参阅完整的 [Databricks Runtime 6.6](../../runtime/6.6.md) 发行说明。

## <a name="job-clusters-now-tagged-with-job-name-and-id"></a>作业群集现已用作业名称和 ID 进行标记

**2020 年 5 月 5 日至 12 日：版本 3.19**

作业群集会用作业名称和 ID 自动进行标记。 这些标记显示在可计费使用情况报告中，以便你可轻松地按作业确定 DBU 使用情况的原因并识别异常。 标记将按照群集标记规范进行清理，例如按允许的字符、最大大小和最大标记数。 作业名称包含在 `RunName` 标记中，作业 ID 包含在 `JobId` 标记中。

## <a name="restore-deleted-notebook-cells"></a>还原已删除的笔记本单元

**2020 年 5 月 5 日至 12 日：版本 3.19**

现可使用 (`Z`) 键盘快捷方式或选择“编辑”>“撤消删除单元格”来还原已删除的单元格。

## <a name="jobs-pending-queue-limit"></a>作业挂起队列限制

**2020 年 5 月 5 日至 12 日：版本 3.19**

工作区现在限制为 1000 个活动（正在运行和挂起的）作业运行。 由于工作区限制为 150 个并发（正在运行）作业运行，因此一个工作区的挂起队列中最多可以有 850 个运行。