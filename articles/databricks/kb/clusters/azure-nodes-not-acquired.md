---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 06/08/2020
title: 群集启动缓慢，缺少节点 - Azure Databricks
description: 了解如何解决启动 <a Databricks> 群集时出现的“无法获取节点”错误
category: Cluster Creation
author: adampavDB
db-author: adam.pavlacka@databricks.com
ms.openlocfilehash: 5dfedaca05cc02f1f8f40c4010356267f00450fd
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589776"
---
# <a name="slow-cluster-launch-and-missing-nodes"></a>群集启动缓慢，缺少节点

## <a name="problem"></a>问题

群集需要很长时间才能启动，并显示类似于以下内容的错误消息：

```console
Cluster is running but X nodes could not be acquired
```

## <a name="cause"></a>原因

预配 Azure VM 通常需要 2-4 分钟的时间，但如果无法同时预配群集中的所有 VM，则可能会延迟群集创建。 这是由于 <Databricks> 必须在一段时间内重新发出 VM 创建请求。

## <a name="solution"></a>解决方案

如果在没有所有节点的情况下启动群集，<Databricks> 将自动尝试获取其他节点，并在可用时更新群集。

要解决此问题，应配置具有更大实例类型和更少节点数的群集。