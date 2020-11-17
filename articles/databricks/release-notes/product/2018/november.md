---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 2018 年 11 月 - Azure Databricks
description: Azure Databricks 新功能和改进的 2018 年 11 月发行说明。
ms.openlocfilehash: 9b4fcd691b8cd05687befe8e2700055db88c7283
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329231"
---
# <a name="november-2018"></a>2018 年 11 月

这些功能和 Azure Databricks 平台的改进已于 2018 年 11 月发布。

> [!NOTE]
>
> 发布分阶段进行。 在初始发布日期后，可能最长需要等待一周，你的 Azure Databricks 帐户才会更新。

## <a name="library-ui"></a><a id="library-ui"> </a><a id="library-ui-update"> </a>库 UI

> [!IMPORTANT]
>
> 此更新已于 2018 年 12 月 7 日还原。

2018 年 11 月 27 日- 12 月 4 日：版本 2.85

在此版本中，库 UI 已经得到了显著改进。

Azure Databricks UI 现在支持工作区库和群集附加的库。 工作区库存在于工作区中，可附加到一个或多个群集。 群集附加库是只存在于其附加到的群集的上下文中的库。 此外：

* 现在可以从上传到对象存储的文件创建库。
* 现在可以从库详细信息页和群集的“库”选项卡中附加和分离库。
* 现在，使用 API 安装的库会在群集的“库”选项卡中显示。

## <a name="custom-spark-heap-memory-settings-enabled"></a>已启用自定义 Spark 堆内存设置

2018 年 11 月 27 日- 12 月 4 日：版本 2.85

以下 Spark 内存设置现在生效：

* `spark.executor.memory`
* `spark.driver.memory`

> [!IMPORTANT]
>
> * Azure Databricks 具有在每个节点上运行的服务，因此 Spark 所允许的最大内存小于云服务提供商报告的 VM 的内存容量。 如果要为 Spark 提供执行程序或驱动程序的最大堆内存，请勿指定 `spark.executor.memory` 或 `spark.driver.memory`。
> * 某些以前无效但被忽略的群集配置可能导致群集故障。

## <a name="jobs-and-idle-execution-context-eviction"></a>作业和空闲执行上下文逐出

2018 年 11 月 27 日- 12 月 4 日：版本 2.85

作业现在自动逐出空闲执行上下文。 请参阅[执行上下文](../../../notebooks/notebooks-manage.md#execution-context)。 若要最大程度地减少自动逐出，Azure Databricks 建议为作业和交互式工作负载使用不同的群集。

## <a name="databricks-runtime-50-for-machine-learning-beta-release"></a>现已发布用于机器学习的 Databricks Runtime 5.0 (Beta)

**2018 年 11 月 19 日**

Databricks Runtime 5.0 ML (Beta) 为机器学习和数据科学提供了随时可用的环境。 它包含多个热门库，其中包括 TensorFlow、Keras 和 XGBoost。 它还支持使用 Horovod 进行分布式 TensorFlow 训练。 Databricks Runtime 5.0 ML 是基于 Databricks Runtime 5.0 构建的。 Databricks Runtime 5.0 ML 包括以下新功能：

* HorovodRunner，运行使用 Horovod 的分布式深度学习训练作业。 请参阅[分布式训练](../../../applications/machine-learning/train-model/distributed-training/index.md)。
* 用于包管理的 [Conda](https://conda.io/docs/) 支持。
* [MLeap](../../../applications/machine-learning/model-export/mleap-model-export.md) 集成。
* [GraphFrames](../../../spark/latest/graph-analysis/graphframes/index.md) 集成。

请参阅 [Databricks Runtime 5.0 ML（Beta 版本）](../../runtime/5.0ml.md)的完整发行说明。

## <a name="databricks-runtime-50-release"></a>现已发布 Databricks Runtime 5.0

**2018 年 11 月 8 日**

Databricks Runtime 5.0 现已推出。  Databricks Runtime 5.0 包括 Apache Spark 2.4.0、新的 Delta Lake 和结构化流功能和升级以及已升级的 Python、R、Java 和 Scala 库。 有关详细信息，请参阅 [Databricks Runtime 5.0（不受支持）](../../runtime/5.0.md)。

在 Databricks Runtime 5.0 上，一旦群集达到最大上下文限制 (145)，Azure Databricks 立即逐出空闲执行上下文。 请参阅[执行上下文](../../../notebooks/notebooks-manage.md#execution-context)。

## <a name="displayhtml-support-for-unrestricted-loading-of-third-party-content"></a>`displayHTML` 支持第三方内容的无限制加载

2018 年 11 月 6 日 - 13 日：版本 2.84

以前，`displayHTML` iframe 沙盒缺失 [allow-same-origin](https://www.w3schools.com/tags/att_iframe_sandbox.asp) 属性。 这意味着 iframe 的来源为空，这并不适合[跨源 XHR 请求](https://www.w3schools.com/tags/att_iframe_sandbox.asp)、cookie 或访问嵌入的 iframe。 在此版本中，`displayHTML` iframe 是从新域 `databricksusercontent.com` 提供的，iframe 沙盒现在包含 `allow-same-origin` 属性。

如果你已在使用 displayHTML，则无需改变对 displayHTML 的使用。

需要可在浏览器中访问 `databricksusercontent.com`。 如果它当前被企业网络阻止，IT 人员需要将它加入允许列表。