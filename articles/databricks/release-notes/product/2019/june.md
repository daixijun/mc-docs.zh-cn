---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 01/02/2020
title: 2019 年 6 月 - Azure Databricks
description: 新 Azure Databricks 功能和改进的 2019 年 6 月发行说明。
ms.openlocfilehash: ba31a51c103fccf15d46657948d0acfee972c73a
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329402"
---
# <a name="june-2019"></a>2019 年 6 月

这些功能和 Azure Databricks 平台改进已于 2019 年 6 月发布。

> [!NOTE]
>
> 发布分阶段进行。 在初始发布日期后，可能最长需要等待一周，你的 Azure Databricks 帐户才会更新。

## <a name="lsv2-instance-support-is-generally-available"></a>Lsv2 实例支持已推出正式版

**2019 年 6 月 24 日至 26 日：版本 2.100**

Azure Databricks 现在为 [Lsv2 VM 系列](https://azure.microsoft.com/blog/announcing-the-general-availability-of-lsv2-series-azure-virtual-machines/)提供全面支持，以实现高吞吐量和高 IOPS 工作负载。

## <a name="rstudio-integration-no-longer-limited-to-high-concurrency-clusters"></a>RStudio 集成不再局限于高并发性群集

**2019 年 6 月 6 日至 11 日：版本 2.99**

现在，你可以在 Azure Databricks 中的[标准群集](../../../clusters/configure.md#cluster-mode)上启用 RStudio Server，以及已支持的高并发群集。 无论采用何种群集模式，RStudio Server 集成都将继续要求你为群集禁用[自动终止](../../../clusters/clusters-manage.md#automatic-termination)选项。 请参阅 [Azure Databricks 上的 RStudio](../../../spark/latest/sparkr/rstudio.md)。

## <a name="mlflow-10"></a>MLflow 1.0

**2019 年 6 月 3 日**

[MLflow](https://www.mlflow.org/) 是用于管理完整的机器学习生命周期的开放源代码平台。 借助 MLflow，数据科学家可以在本地或在云中跟踪和共享试验、在各种框架上打包和共享模型，以及几乎可随处部署模型。

今天，我们很高兴地宣布 MLflow 1.0 版本的发布。 1\.0 版本不仅能标记 API 的成熟度和稳定性，还增加了许多常见请求的功能和改进：

* CLI 已重新组织，并且现在具有用于项目、模型、db（跟踪数据库）和服务器（跟踪服务器）的专用命令。
* 跟踪服务器搜索支持 `SQL WHERE` 子句的简化版本。 除了支持运行指标和参数外，搜索功能还得到了增强，可支持某些运行属性以及用户和系统标记。
* 添加了对跟踪 API 中 x 坐标的支持。 MLflow UI 可视化组件现在还支持针对提供的 x 坐标值绘制指标。
* 添加了一个 `runs/log-batch` REST API 终结点，以及用于通过单个 API 请求记录多个指标、参数和标记的 Python、R 和 Java 方法。
* 对于跟踪，Windows 上现在支持 MLflow 1.0 客户端。
* 添加了对 HDFS 作为项目存储后端的支持。
* 添加命令以生成 Docker 容器，该容器的默认入口点为容器内端口 8080 处的指定 MLflow Python 函数模型提供服务。
* 添加实验性 [ONNX](https://onnx.ai/) 模型风格。

可以在 [MLflow 更改日志](https://github.com/mlflow/mlflow/blob/master/CHANGELOG.rst)中查看更改的完整列表。

## <a name="databricks-runtime-54-with-conda-beta"></a>带有 Conda 的 Databricks Runtime 5.4 (Beta)

**2019 年 6 月 3 日**

> [!IMPORTANT]
>
> 带有 Conda 的 Databricks Runtime 以 [beta 版本](../../release-types.md#runtime-releases)提供。 在即将发布的 Beta 版本中，支持的环境的内容可能会发生变化。 更改可能包括包列表或已安装包的版本的列表。 带有 Conda 的 Databricks Runtime 5.4 是基于 [Databricks Runtime 5.4（不受支持）](../../runtime/5.4.md)构建的。

很高兴引入带有 Conda 的 Databricks Runtime 5.4，它可以让你利用 Conda 来管理 Python 库和环境。 此运行时在创建群集时提供两个 Conda 根环境选项：

* Databricks Standard 环境包括许多常用 Python 包的更新版本。 此环境旨在替代在 Databricks Runtime 上运行的现有笔记本。 这是基于 Databricks Conda 的默认运行时环境。
* **Databricks Minimal** 环境包含 PySpark 和 Databricks Python 笔记本功能所需的最小数量的包。 这个环境非常适合使用各种 Python 包进行运行时自定义。

请参阅[带有 Conda 的 Databricks Runtime 5.4（beta 版本）](../../runtime/5.4conda.md) 的完整发行说明。

## <a name="databricks-runtime-54-for-machine-learning"></a>用于机器学习的 Databricks Runtime 5.4

**2019 年 6 月 3 日**

Databricks Runtime 5.4 ML 是基于 [Databricks Runtime 5.4（不受支持）](../../runtime/5.4.md)构建的。 它包含许多常见的机器学习库，包括 TensorFlow、PyTorch、Keras 和 XGBoost，并使用 Horovod 提供分布式 TensorFlow 训练。

它包括以下新增功能：

* MLlib 与 MLflow 集成（公共预览版）。
* 预安装了新 SparkTrials 类的 Hyperopt（公共预览版）。
* 从 Horovod 发送到 Spark 驱动程序节点的 HorovodRunner 输出现在显示在笔记本单元中。
* 已预安装的 XGBoost Python 包。

有关详细信息，请参阅 [Databricks Runtime 5.4 ML（不受支持）](../../runtime/5.4ml.md)。

## <a name="databricks-runtime-54"></a>Databricks Runtime 5.4

**2019 年 6 月 3 日**

Databricks Runtime 5.4 现已推出。  Databricks Runtime 5.4 包括 Apache Spark 2.4.2、已升级的 Python、R、Java 和 Scala 库，以及以下新增功能：

* Databricks 上的 Delta Lake 增加了自动优化（公共预览版）
* 将最喜欢的 IDE 和笔记本服务器与 Databricks Connect 一起使用
* 库实用工具已公开发布
* 二进制文件数据源

有关详细信息，请参阅 [Databricks Runtime 5.4（不受支持）](../../runtime/5.4.md)。