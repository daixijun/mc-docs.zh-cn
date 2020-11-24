---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 06/08/2020
title: IP 地址限制阻止创建群集 - Azure Databricks
description: 了解如何修复启动 <a Databricks> 群集时出现的公共 IP 地址配额限制云提供程序启动错误
category: Cluster Creation
author: adampavDB
db-author: adam.pavlacka@databricks.com
ms.openlocfilehash: a01ad49c0ffbe9d75ccff9cf3f9e1a2f75a85481
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589805"
---
# <a name="ip-address-limit-prevents-cluster-creation"></a>IP 地址限制阻止创建群集

## <a name="problem"></a>问题

群集创建失败，当你将鼠标悬停在群集状态上时，会显示有关云提供程序错误的消息。

```console
Cloud Provider Launch Failure: A cloud provider error was encountered while setting up the cluster.
```

[查看群集事件日志](https://docs.databricks.com/clusters/clusters-manage.html#view-a-cluster-event-log)以获取更多详细信息时，会看到一条有关 `publicIPAddresses` 限制的消息。

```console
ResourceQuotaExceeded Azure error message: Creating the resource of type 'Microsoft.Network/publicIPAddresses' would exceed the quota of '800' resources of type 'Microsoft.Network/publicIPAddresses' per resource group. The current resource count is '800', please delete some resources of this type before creating a new one.'
```

## <a name="cause"></a>原因

Azure 订阅存在公共 IP 地址限制，这会限制你可以使用的公共 IP 地址数。 这是硬性限制。 如果尝试启动的群集会导致帐户超出公共 IP 地址配额，则群集启动会失败。

## <a name="solution"></a>解决方案

你可以释放资源或请求为帐户增加配额。

* 停止非活动群集以释放要使用的公共 IP 地址。
* 建立 Azure 支持案例，请求为你的订阅增加公共 IP 地址配额限制。