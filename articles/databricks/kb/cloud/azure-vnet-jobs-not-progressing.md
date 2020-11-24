---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 作业未在工作区中进行 - Azure Databricks
description: '了解如何使用 Azure 防火墙对虚拟网络中的 Azure Databricks 工作区进行故障排除。 “警告: TaskSchedulerImpl 初始作业未接受任何资源”错误消息。'
ms.openlocfilehash: 26aa4321eaab9c2275c8e65a452e0c5a1cd4c3fc
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589809"
---
# <a name="jobs-are-not-progressing-in-the-workspace"></a>作业未在工作区中进行

## <a name="problem"></a>问题

作业无法在工作区中的任何群集上运行。

## <a name="cause"></a>原因

如果更改了现有工作区的 VNet，则可能会发生这种情况。 不支持更改现有 Azure Databricks 工作区的 VNet。

有关更多详细信息，请参阅[在 VNet 中部署 Azure Databricks](/databricks/administration-guide/cloud-configurations/azure/vnet-inject)。

## <a name="solution"></a>解决方案

1. 在 Azure Databricks UI 中打开[群集驱动程序日志](/databricks/clusters/clusters-manage#--cluster-driver-and-worker-logs)。
2. 搜索以下警告消息：

   ```console
   19/11/19 16:50:29 WARN TaskSchedulerImpl: Initial job has not accepted any resources; check your cluster UI to ensure that workers are registered and have sufficient resources
   19/11/19 16:50:44 WARN TaskSchedulerImpl: Initial job has not accepted any resources; check your cluster UI to ensure that workers are registered and have sufficient resources
   19/11/19 16:50:59 WARN TaskSchedulerImpl: Initial job has not accepted any resources; check your cluster UI to ensure that workers are registered and have sufficient resources
   ```

   如果出现此错误，则可能是 Azure Databricks 工作区的 VNet 发生了更改。

3. 还原更改可还原在创建 Azure Databricks 工作区时使用的原始 VNet 配置。
4. 重启正在运行的群集。
5. 重新提交作业。
6. 验证作业是否正在获取资源。