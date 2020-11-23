---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/11/2020
title: 版本控制 - Azure Databricks
description: 了解 Databricks ML 模型导出支持的 Apache Spark 版本以及模型导出版本控制策略。
ms.openlocfilehash: 3f9dce8d1223a6306eb40fcb35c3dfde77a79371
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589565"
---
# <a name="versioning"></a><a id="model-versioning"> </a><a id="versioning"> </a>版本控制

本部分介绍 Databricks ML 模型导出支持的 Apache Spark 版本和模型导出版本控制策略。

## <a name="supported-databricks-runtime-versions"></a>支持的 Databricks Runtime 版本

若要将模型导出与 Databricks Runtime 4.0 或更低版本中的模型一起使用，可使用 [MLlib 持久性](https://spark.apache.org/docs/latest/ml-pipeline.html#ml-persistence-saving-and-loading-pipelines)保存较低版本中的模型，然后将其加载到 Databricks Runtime 4.0 或更高版本中。
然后，可使用模型导出来导出模型，以便进行评分。

## <a name="databricks-ml-model-export-versioning"></a>Databricks ML 模型导出版本控制

本部分介绍 Databricks ML 模型导出及其配套库 `dbml-local` 的版本控制策略和向后兼容性保证。

> [!TIP]
>
> 若要查找与导出的模型一起使用的最佳 `dbml-local` 版本，请查看 [Databricks 运行时发行说明](../../../release-notes/runtime/index.md)以获取 Databricks Runtime 的 Apache Spark 版本，并找到带有该 Apache Spark 版本后缀的最新 `dbml-local` 版本。

### <a name="apache-spark-mllib-version-policy"></a>Apache Spark MLlib 版本策略

由于 Databricks ML 模型导出和 `dbml-local` 旨在复制 MLlib 行为，因此模型导出版本控制策略基于 [ML 持久性](https://spark.apache.org/docs/latest/ml-pipeline.html#ml-persistence-saving-and-loading-pipelines)的 MLlib 策略。
通常，MLlib 保持向后兼容性。 但存在几种例外情况。

**模型持久性**

使用 Spark 版本 `a.b.c` 中 Apache Spark ML 持久性保存的模型或管道是否可通过 Spark 版本 `x.y.z` 进行加载？

* 主版本：无法保证，但尽力而为。
* 次要版本和补丁版本：是，这些都向后兼容。
* 有关格式的说明：不能保证稳定的持久性格式，但是模型加载本身是设计为向后兼容的。

**模型行为**

Spark 版本 `a.b.c` 中的模型或管道在 Spark 版本 `x.y.z` 中的行为是否相同？

* 主版本：无法保证，但尽力而为。
* 次要版本和补丁版本：除 bug 修复外，行为相同。

对于模型持久性和模型行为，[Spark 版本发行说明](https://spark.apache.org/docs/latest/ml-guide.html)中报告了次要版本或补丁版本中的所有中断性变更。
如果发行说明中未报告出现中断情况，则应将其视为要修复的 bug。

### <a name="databricks-ml-model-export-guarantees"></a>Databricks ML 模型导出保证

通常，Databricks ML 模型导出和 `dbml-local` 旨在提供与 MLlib 相同的保证：

* 如果 MLlib 在 Spark 版本间保持一致的行为，则 `dbml-local` 也会如此。
* 如果 MLlib 在不同 Spark 版本间的行为有变化，则 `dbml-local` 将为新的 Spark 版本提供更新版本。

评分库 `dbml-local` 的版本控制为 `x.y.z_sparkA.B`（例如 `0.1.3_spark2.2`）。 此版本字符串包含 2 个部分：

* `dbml-local` 版本号（例如 `0.1.3`）
  * 这是库版本号，遵循[语义版本控制](https://semver.org/) (`major.minor.patch`)。
  * 对于模型来说，补丁版本和次要版本的行为是相同的。
* Apache Spark 版本后缀（例如 `_spark2.2`）
  * 添加此后缀是为了方便选择与用于创建模型的 Apache Spark 版本相匹配的 `dbml-local` 版本。
  * 例如，对于版本为 `0.1.3_spark2.2` 的库，我们保证：
    * 可加载从先前的 Spark 次要版本和补丁版本导出的模型，这些版本是指：Spark 2.0、2.1、2.2。
    * 行为与后缀的 Spark 次要版本（在本例中为 Spark 2.2）相同。
    * 如果 Spark 补丁版本包含针对 bug 修复的行为更改，我们将在 `dbml-local` 发布文档中对此进行说明。