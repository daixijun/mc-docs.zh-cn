---
title: Apache Ambari UI 显示 Azure HDInsight 中的主机和服务已关闭
description: Apache Ambari UI 显示 Azure HDInsight 中的主机和服务已关闭
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: v-yiso
origin.date: 08/02/2019
ms.date: 09/23/2019
ms.openlocfilehash: d026d3678d0eda40db35c46f024962fd6f4f31b6
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96300844"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>方案：Apache Ambari UI 显示 Azure HDInsight 中的主机和服务已关闭

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

Apache Ambari UI 可以访问，但 UI 显示几乎所有服务都已关闭，所有主机都显示检测信号丢失。

## <a name="cause"></a>原因

在大多数情况下，这是 Ambari 服务器未在活动头节点上运行的问题。 检查哪个头节点是活动头节点，并确保 Ambari 服务器在正确的头节点上运行。 请勿手动启动 Ambari 服务器，让故障转移控制器服务负责在正确的头节点上启动 Ambari 服务器。 重新启动活动头节点以强制进行故障转移。

网络问题也可能导致此问题。 从每个群集节点，看看是否可以 ping 通 `headnodehost`。 在极少数情况下，任何群集节点都无法连接到 `headnodehost`：

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>解决方法

通常，重新启动活动头节点会缓解此问题。 如果未缓解，请与 HDInsight 支持团队联系。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]