---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 07/22/2020
title: 2019 年 8 月 - Azure Databricks
description: Azure Databricks 新增功能和改进功能的 2019 年 8 月发行说明。
ms.openlocfilehash: 48735ed4a7d11847d4da70702409e035ebdb46ac
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329438"
---
# <a name="august-2019"></a>2019 年 8 月

这些功能和 Azure Databricks 平台的改进功能已于 2019 年 8 月发布。

> [!NOTE]
>
> 发布分阶段进行。 在初始发布日期后，可能最长需要等待一周，你的 Azure Databricks 帐户才会更新。

## <a name="new-region-uae-north"></a>新区域：阿拉伯联合酋长国北部

**2019 年 8 月 27 日**

Azure Databricks 现已在阿拉伯联合酋长国北部（迪拜）推出。

## <a name="workspace-library-installation-enhancement"></a>工作区库安装增强

**2019 年 8 月 27 日至 9 月 3 日：版本 3.1**

管理员现在可以通过群集的“库”选项卡在所有群集上安装库。

> [!div class="mx-imgBorder"]
> ![安装群集库](../../../_static/images/clusters/cluster-library.png)

有关详细信息，请参阅[工作区库](../../../libraries/cluster-libraries.md#install-workspace-libraries)。

## <a name="clusters-ui-now-reflects-more-consistent-_interactive_-and-_automated_-cluster-terminology"></a>群集 UI 现在会反映更一致的交互式和自动化群集术语

**2019 年 8 月 27 日至 9 月 3 日：版本 3.1**

为了明确你为作业使用哪一种类型的群集（自动或交互式），“群集”列表和作业配置群集 UI 进行了更新。

在“群集”列表中，“作业群集”子列表已重命名为“自动群集”，以反映为作业创建的群集按自动费率收费这一事实。

> [!div class="mx-imgBorder"]
> ![自动群集](../../../_static/images/clusters/automated-clusters.png)

为作业配置群集时，“群集类型”菜单选项“新群集”已重命名为“新自动群集”，“现有群集”已重命名为“现有交互式群集”。
这些更改反映了这样一个事实：如果你选择新群集，它将按自动费率收费；如果你选择现有群集，它将按交互费率收费。

> [!div class="mx-imgBorder"]
> ![作业自动群集](../../../_static/images/clusters/job-automated-cluster.png)

## <a name="databricks-runtime-55-and-databricks-runtime-55-ml-are-lts"></a>Databricks Runtime 5.5 和 Databricks Runtime 5.5 ML 现已推出 LTS 版本

**2019 年 8 月 27 日**

Databricks Runtime 5.5 和 Databricks Runtime 5.5 ML 被确定为长期支持 (LTS) 运行时。 Azure Databricks 为 LTS 运行时提供整整两年的支持。 有关详细信息，请参阅 [Databricks Runtime 发行说明](../../runtime/index.md#runtime-release-notes)。

## <a name="instance-allocation-notifications-for-pools"></a>针对池的实例分配通知

**2019 年 8 月 13 日至 20 日：版本 2.104**

现在，如果池未能分配[最小空闲实例数](../../../clusters/instance-pools/configure.md#instance-pool-sizing)，则会通知你。 池详细信息和统计信息页会显示一条错误消息，指出失败的实例数。 你可以单击错误消息以查看有关每个挂起实例失败的详细信息。

## <a name="new-cluster-events"></a>新的群集事件

**2019 年 8 月 13 日至 20 日：版本 2.104**

群集事件现在会在群集[固定](../../../clusters/clusters-manage.md#cluster-pin)或取消固定时生成。

请参阅 [ClusterEventType](../../../dev-tools/api/latest/clusters.md#clustereventsclustereventtype)。

## <a name="mlflow-updates"></a>MLflow 更新

**2019 年 8 月 13 日至 20 日：版本 2.104**

笔记本中的“运行”边栏现在支持按指标或参数对运行排序。

MLflow 1.2 已于 2019 年 8 月 12 日发布。 有关详细信息，请参阅 [MLflow 1.2 Changelog](https://github.com/mlflow/mlflow/blob/master/CHANGELOG.rst#12-2019-08-12)。