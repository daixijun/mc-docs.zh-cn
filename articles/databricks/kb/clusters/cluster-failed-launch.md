---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 05/07/2020
title: 群集无法启动 - Azure Databricks
description: 了解如何解决无法启动 Azure Databricks 群集的问题。
ms.openlocfilehash: 9d709082c4d28f0ce20b3fd55d4122c7e296e655
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589774"
---
# <a name="cluster-failed-to-launch"></a>群集无法启动

本文介绍了群集无法启动的几种情况，并根据在日志中找到的错误消息为每种情况提供了故障排除步骤。

## <a name="cluster-timeout"></a>群集超时

错误消息：

```console
Driver failed to start in time

INTERNAL_ERROR: The Spark driver failed to start within 300 seconds

Cluster failed to be healthy within 200 seconds
```

### <a name="cause"></a>原因

如果群集连接到外部 Hive 元存储并尝试从 `maven` 存储库下载所有 Hive 元存储库，则群集可能无法启动。 群集下载将近 200 个 JAR 文件，包括依赖项。 如果 Azure Databricks 群集管理器无法在 5 分钟内确认驱动程序已就绪，则群集无法启动。 出现这种情况的原因可能是 JAR 下载时间太长。

### <a name="solution"></a>解决方案

将 Hive 库存储在 DBFS 中，并从 DBFS 位置本地访问它们。 请参阅 [Spark 选项](/databricks/data/metastores/external-hive-metastore#spark-options)。

## <a name="global-or-cluster-specific-init-scripts"></a>全局或特定于群集的初始化脚本

错误消息：

```console
The cluster could not be started in 50 minutes. Cause: Timed out with exception after <xxx> attempts
```

### <a name="cause"></a>原因

在群集启动阶段期间运行的初始化脚本向每个辅助角色计算机发送 RPC（远程过程调用），以便在本地运行脚本。 在该过程继续之前，所有 RPC 都必须返回其状态。 如果有任何 RPC 遇到问题，而未进行响应（例如由于暂时性网络问题），则可能会遇到 1 小时超时，从而导致群集设置作业失败。

### <a name="solution"></a>解决方案

请使用[群集范围初始化脚本](/databricks/clusters/init-scripts#cluster-scoped-init-script)，而不是全局或群集命名初始化脚本。 使用群集范围初始化脚本时，Azure Databricks 不会使用 RPC 的同步阻止来提取初始化脚本执行状态。

## <a name="too-many-libraries-installed-in-cluster-ui"></a>群集 UI 中安装了太多的库

错误消息：

```console
Library installation timed out after 1800 seconds. Libraries that are not yet installed:
```

### <a name="cause"></a>原因

这通常是网络问题造成的间歇性问题。

### <a name="solution"></a>解决方案

通常情况下，可以通过重新运行作业或重启群集来解决此问题。

库安装程序配置为 3 分钟后超时。 提取和安装 jar 时，可能会由于网络问题发生超时。 若要缓解此问题，可将库从 `maven` 下载到 DBFS 位置，并从该位置进行安装。

## <a name="cloud-provider-limit"></a>云提供商限制

错误消息：

```console
Cluster terminated. Reason: Cloud Provider Limit
```

### <a name="cause"></a>原因

此错误通常由云提供商返回。

### <a name="solution"></a>解决方案

请参阅[群集意外终止](/databricks/kb/clusters/termination-reasons)中的云提供商错误信息。

## <a name="cloud-provider-shutdown"></a>云提供商关闭

错误消息：

```console
Cluster terminated. Reason: Cloud Provider Shutdown
```

### <a name="cause"></a>原因

此错误通常由云提供商返回。

### <a name="solution"></a>解决方案

请参阅[群集意外终止](/databricks/kb/clusters/termination-reasons)中的云提供商错误信息。

## <a name="instances-unreachable"></a>Instances unreachable（实例不可访问）

错误消息：

```console
Cluster terminated. Reason: Instances Unreachable

An unexpected error was encountered while setting up the cluster. Please retry and contact Azure Databricks if the problem persists. Internal error message: Timeout while placing node
```

### <a name="cause"></a>原因

此错误通常由云提供商返回。 通常，将 Azure Databricks 工作区[部署到你自己的虚拟网络 (VNet)](/databricks/administration-guide/cloud-configurations/azure/vnet-inject)（而不是启动新 Azure Databricks 工作区时创建的默认 VNet）时，会发生这种情况。 如果已部署工作区的虚拟网络已对等互连或与本地资源建立了 ExpressRoute 连接，则当 Azure Databricks 尝试创建群集时，虚拟网络无法与群集节点建立 `ssh` 连接。

### <a name="solution"></a>解决方案

添加用户定义的路由 (UDR)，以使 Azure Databricks 控制平面能够通过 `ssh` 方式访问群集实例、Blob 存储实例和项目资源。 此自定义 UDR 允许出站连接且不会干扰群集创建。 有关详细 UDR 说明，请参阅[步骤 3：创建用户定义的路由，并将其与 Azure Databricks 虚拟网络子网关联](/databricks/administration-guide/cloud-configurations/azure/on-prem-network#create-routes)。 有关与 VNet 相关的详细故障排除信息，请参阅[故障排除](/databricks/administration-guide/cloud-configurations/azure/vnet-inject#troubleshooting)。