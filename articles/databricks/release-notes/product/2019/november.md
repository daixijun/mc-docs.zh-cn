---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 06/03/2020
title: 2019 年 11 月 - Azure Databricks
description: 新 Azure Databricks 功能和改进的 2019 年 11 月发行说明。
ms.openlocfilehash: 8825a3b4c90022fee898110c8fccf83b23dcbbd4
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329328"
---
# <a name="november-2019"></a>2019 年 11 月

这些功能和 Azure Databricks 平台的改进已于 2019 年 11 月发布。

> [!NOTE]
>
> 发布分阶段进行。 Azure Databricks 帐户可能要等到初始发布日期后的一周或更长时间才会更新。

## <a name="databricks-runtime-62-ml-beta"></a>Databricks Runtime 6.2 ML Beta

**2019 年 11 月 15 日**

Databricks Runtime 6.2 ML Beta 引入了许多库升级，其中包括：

* TensorFlow 和 TensorBoard：1.14.0 到 1.15.0。
* PyTorch：1.2.0 到 1.3.0。
* tensorboardX：1.8 到 1.9。
* MLflow：1.3.0 到 1.4.0。
* Hyperopt：0.2-db1 与 Azure Databricks MLflow 集成。
* mleap-databricks-runtime 到 0.15.0，包括 mleap-xgboost-runtime。

有关详细信息，请参阅完整的 [Databricks Runtime 6.2 ML（不受支持）](../../runtime/6.2ml.md)发行说明。

## <a name="databricks-runtime-62-beta"></a>Databricks Runtime 6.2 Beta

**2019 年 11 月 15 日**

Databricks Runtime 6.2 Beta 引入了新功能、改进和许多 bug 修复，包括：

* 优化了 Delta Lake 仅插入合并

有关详细信息，请参阅完整的 [Databricks Runtime 6.2（不受支持）](../../runtime/6.2.md)发行说明。

## <a name="configure-clusters-with-your-own-container-image-using-databricks-container-services"></a>通过 Databricks 容器服务使用自己的容器映像配置群集

之前宣布的 Azure Databricks 的 Databricks 容器服务的正式发布版现预期于 2020 年年初发布。 如果你有兴趣尝试此服务的预览版本，以便使用自己的容器映像配置群集，请与 Azure Databricks 代表联系。

## <a name="cluster-detail-now-shows-only-cluster-id-in-the-http-path"></a>群集详细信息现在仅显示 HTTP 路径中的群集 ID

2019 年 11 月 19 日 - 12 月 3 日：版本 3.6

使用 JDBC/ODBC 将 BI 工具连接到 Azure Databricks 时，应使用 HTTP 路径的群集 ID 变体，因为它是唯一的。 群集详细信息页上的 [JDBC/ODBC 选项卡](../../../integrations/bi/jdbc-odbc-bi.md#jdbc-odbc-params)上不再显示群集名称选项。

## <a name="secrets-referenced-by-spark-configuration-properties-and-environment-variables-public-preview"></a>Spark 配置属性和环境变量引用的机密（公共预览版）

2019 年 11 月 7 日

在 Databricks Runtime 6.1 及更高版本中可用。

自 [11 月 7 日 Databricks Runtime 6.1 的维护更新](../../runtime/maintenance-updates.md#61)起，在 Spark 配置属性或环境变量中引用[机密](../../../security/secrets/index.md)的功能以公开预览版发布。 有关详细信息，请参阅 [Spark 配置属性和环境变量中的机密路径](../../../security/secrets/secrets.md#spark-conf-env-var)。