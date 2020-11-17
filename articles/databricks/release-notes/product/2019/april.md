---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/13/2020
title: 2019 年 4 月 - Azure Databricks
description: 新 Azure Databricks 功能和改进的 2019 年 4 月发行说明。
ms.openlocfilehash: 05727efb921f36837234e5c458772f5b57e4cc21
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329230"
---
# <a name="april-2019"></a>2019 年 4 月

这些功能和 Azure Databricks 平台的改进已于 2019 年 4 月发布。

> [!NOTE]
>
> 发布是分阶段进行的。 在初始发布日期后，可能最长需要等待一周，你的 Azure Databricks 帐户才会更新。

## <a name="mlflow-on-azure-databricks-ga"></a>Azure Databricks 上的 MLflow（正式版）

2019 年 4 月 25 日

Azure Databricks 上托管的 MLflow 现已正式发布。 Azure Databricks 上的 MLflow 提供了一个 MLflow 的托管版本，该版本与 Databricks 安全模型和交互式工作区完全集成。 请参阅 [MLflow](../../../applications/mlflow/index.md#mlflow-guide)。

## <a name="delta-lake-on-azure-databricks"></a>Azure Databricks 上的 Delta Lake

**2019 年 4 月 24 日**

Databricks 已经开放了 [Delta Lake](https://delta.io) 项目的源代码。 Delta Lake 是一个存储层，它通过在各写入操作之间进行乐观并发控制以及在写入过程中为一致读取进行快照隔离，来提供 ACID 事务，从而为基于 HDFS 和云存储构建的数据湖带来可靠性。 Delta Lake 还提供了内置的数据版本控制，以便于轻松回滚和重现报告。

> [!NOTE]
>
> 以前称为 Databricks Delta 的产品现在是 Delta Lake 开放源代码项目，还包括可用于 Azure Databricks 的优化。 请参阅 [Delta Lake](../../../delta/index.md)。

## <a name="mlflow-runs-sidebar"></a>MLflow 运行边栏

2019 年 4 月 9 日 - 16 日：版本 2.95

现在可以在笔记本旁边的边栏中查看 MLflow 运行和生成这些运行的笔记本修订版本。

> [!div class="mx-imgBorder"]
> ![笔记本边栏中的 MLflow 运行](../../../_static/images/mlflow/mlflow-notebook-experiments-python.gif)

请参阅[笔记本试验](../../../applications/mlflow/tracking.md#mlflow-notebook-experiments)。

## <a name="access-azure-data-lake-storage-gen1-and-gen2-automatically-with-your-azure-ad-credentials-ga"></a>使用 Azure AD 凭据自动访问 Azure Data Lake Storage Gen1 和 Gen2（正式版）

2019 年 4 月 9 日 - 16 日：版本 2.95

我们很高兴地宣布，使用登录到 Azure Databricks 时所用的同一 Azure Active Directory (Azure AD) 标识从 Azure Databricks 群集对 Azure Data Lake Storage Gen1 和 Gen2 进行自动身份验证的功能已正式发布。

只需为群集启用 Azure AD 凭据直通身份验证，该群集上运行的命令就可以在 Azure Data Lake Storage Gen1 和 Gen2 中读取和写入数据，而无需配置用于访问存储的服务主体凭据。

有关详细信息，请参阅[使用 Azure Active Directory 凭据直通身份验证来保护对 Azure Data Lake Storage 的访问](../../../security/credential-passthrough/adls-passthrough.md)。

## <a name="databricks-runtime-53-ga"></a>Databricks Runtime 5.3（正式版）

2019 年 4 月 3 日

Databricks Runtime 5.3 现已正式发布。  Databricks Runtime 5.3 包括新增的 Delta Lake 功能和升级，以及升级的 Python、R、Java 和 Scala 库。

主要升级包括：

* Databricks Delta“按时间顺序查看”功能正式发布版
* MySQL 表复制到 Delta，公共预览版
* 针对深度学习工作负载进行了优化的 DBFS FUSE 文件夹
* 笔记本范围内的库改进
* 新增的 Databricks 顾问提示

有关详细信息，请参阅 [Databricks Runtime 5.3（不受支持）](../../runtime/5.3.md)。

## <a name="databricks-runtime-53-ml-ga"></a>Databricks Runtime 5.3 ML（正式版）

2019 年 4 月 3 日

有了适用于机器学习的 Databricks Runtime 5.3，我们完成了 Databricks Runtime ML 的首次正式发布！ Databricks Runtime ML 为机器学习和数据科学提供了随时可用的环境。 它以 Databricks Runtime 为基础构建，并且添加了许多常用的机器学习库，包括 TensorFlow、PyTorch、Keras 和 XGBoost。 它还支持使用 Horovod 进行分布式训练。

此版本基于 Databricks Runtime 5.3 构建，具有更多的库、一些不同的库版本，以及用于 Python 库的 Conda 包管理功能。 自 Databricks Runtime 5.2 ML Beta 以来的主要新增功能包括：

* MLlib 与 MLflow 的集成（个人预览版），它为使用 PySpark 优化算法 `CrossValidator` 和 `TrainValidationSplit` 的模型拟合提供 MLflow 运行的自动记录。

  如果你想参与使用该预览版，请联系 Databricks 客户代表。

* PyArrow、Horovod 和 TensorboardX 库的升级。

  PyArrow 更新添加了在执行基于 Arrow 的转换时可以使用 `BinaryType` 的功能，并使该功能可用于 Pandas UDF。

有关详细信息，请参阅 [Databricks Runtime 5.3 ML（不受支持）](../../runtime/5.3ml.md)。 若要了解如何创建 Databricks Runtime ML 群集，请参阅[适用于机器学习的 Databricks Runtime](../../../runtime/mlruntime.md#mlruntime)。