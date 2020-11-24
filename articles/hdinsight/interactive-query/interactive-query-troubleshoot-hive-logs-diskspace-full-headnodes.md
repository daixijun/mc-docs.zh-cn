---
title: 疑难解答：Apache Hive 日志填满磁盘空间 - Azure HDInsight
description: 本文提供了当 Apache Hive 日志填满 Azure HDInsight 中头节点上的磁盘空间时要遵循的故障排除步骤。
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: v-yiso
ms.reviewer: jasonh
origin.date: 10/05/2020
ms.date: 11/23/2020
ms.openlocfilehash: 24472d6b4439f6f69c574f88345265978b66795b
ms.sourcegitcommit: 5f07189f06a559d5617771e586d129c10276539e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94552209"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>方案：Apache Hive 日志即将填满 Azure HDInsight 的头节点的磁盘空间。

本文介绍了与 Azure HDInsight 群集的头节点上的磁盘空间不足相关的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

在 Apache Hive/LLAP 群集上，不需要的日志占用了头节点上的整个磁盘空间。 这种情况可能会导致以下问题：

- 由于头节点上没有剩余空间，SSH 访问失败。
- Ambari 引发“HTTP 错误:503 服务不可用”。
- HiveServer2 Interactive 无法重启。

发生此问题时，`ambari-agent` 日志会包括以下条目：
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>原因

在高级 Hive log4j 配置中，当前的默认删除计划是根据上次修改日期删除 30 天以前的文件。

## <a name="resolution"></a>解决方法

1. 在 Ambari 门户中转到 Hive 组件摘要，然后选择“配置”选项卡。

2. 转到“高级设置”中的 `Advanced hive-log4j` 部分。

3. 将 `appender.RFA.strategy.action.condition.age` 参数设置为所选的期限。 此示例会将期限设置为 14 天：`appender.RFA.strategy.action.condition.age = 14D`

4. 如果看不到任何相关设置，请追加这些设置：
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
    ```

5. 将 `hive.root.logger` 设置为 `INFO,RFA`，如以下示例所示。 默认设置为 `DEBUG`，这会使日志很大。

    ```
    # Define some default values that can be overridden by system properties
    hive.log.threshold=ALL
    hive.root.logger=INFO,RFA
    hive.log.dir=${java.io.tmpdir}/${user.name}
    hive.log.file=hive.log
    ```

6. 保存配置并重启所需的组件。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区与适当的资源（解答、支持人员和专家）相关联来改善客户体验。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.cn/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”  ，或打开“帮助 + 支持”  中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](/azure-portal/supportability/how-to-create-azure-support-request)。 在 Microsoft Azure 订阅中可以访问订阅管理和计费支持；通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
