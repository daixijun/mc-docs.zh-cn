---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 06/08/2020
title: CPU 核心限制阻止创建群集 - Azure Databricks
description: 了解如何修复当启动 <a Databricks> 群集时出现的 CPU 核心配额限制云提供商启动错误的问题
category: Cluster Creation
author: adampavDB
db-author: adam.pavlacka@databricks.com
ms.openlocfilehash: de45f5e2ae2fed8bbb1e8da1abb0210f00fcc439
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589806"
---
# <a name="cpu-core-limit-prevents-cluster-creation"></a>CPU 内核限制阻止创建群集

## <a name="problem"></a>问题

群集创建失败，这时将鼠标悬停在群集状态上时，会显示有关云提供商错误的消息。

```console
Cloud Provider Launch Failure: A cloud provider error was encountered while setting up the cluster.
```

[查看群集事件日志](https://docs.databricks.com/clusters/clusters-manage.html#view-a-cluster-event-log)以获取更多详细信息时，系统将出现一条有关核心配额限制的消息。

```console
Operation results in exceeding quota limits of Core. Maximum allowed: 350, Current in use: 350, Additional requested: 4.
```

## <a name="cause"></a>原因

Azure 订阅具有 CPU 核心配额限制，它限制了可以使用的 CPU 核心数量。 这是硬性限制。 如果尝试启动群集会导致帐户超出 CPU 核心配额，则群集启动将失败。

## <a name="solution"></a>解决方案

可以释放资源或请求为帐户增加配额。

* 停止非活动群集，释放 CPU 核心，以供使用。
* 打开 Azure 支持案例，并请求增加订阅的 CPU 核心配额限制。