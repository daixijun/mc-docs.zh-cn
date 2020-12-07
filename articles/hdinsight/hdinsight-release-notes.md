---
title: Azure HDInsight 发行说明
description: Azure HDInsight 的最新发行说明。 获取 Hadoop、Spark、R Server、Hive 和更多工具的开发技巧和详细信息。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: cfed7aed9d5b1914448ee7518912c284b9d17cd6
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96300876"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight 发行说明

本文提供有关 **最新** Azure HDInsight 版本更新的信息。 有关较早版本的信息，请参阅 [HDInsight 发行说明存档](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>摘要

Azure HDInsight 是 Azure 中最受企业客户青睐的开源分析服务之一。

如果要订阅发行说明，请查看[此 GitHub 存储库](https://github.com/hdinsight/release-notes/releases)上的版本。

## <a name="release-date-11092020"></a>发行日期：2020/11/09

此版本适用于 HDInsight 3.6 和 HDInsight 4.0。 HDInsight 发行版在几天后即会在所有区域中推出。 此处的发行日期是指在第一个区域中的发行日期。 如果看不到以下更改，请耐心等待，几天后发行版会在你所在的区域推出。

## <a name="new-features"></a>新增功能
### <a name="hdinsight-identity-broker-hib-is-now-ga"></a>HDInsight 标识中介 (HIB) 现已正式发布
为 ESP 群集启用 OAuth 身份验证的 HDInsight 标识中介 (HIB) 现已在此版本中正式发布。 在发布此版本后创建的 HIB 群集将提供最新的 HIB 功能：
- 高可用性 (HA)
- 支持多重身份验证 (MFA)
- 不使用密码哈希同步到 AAD-DS 的联合用户登录。有关详细信息，请参阅 [HIB 文档](https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker)。

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>迁移到 Azure 虚拟机规模集
HDInsight 目前使用 Azure 虚拟机来预配群集。 从此版本开始，该服务将逐渐迁移到 [Azure 虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)。 整个过程可能需要几个月。 迁移区域和订阅后，新创建的 HDInsight 群集将在虚拟机规模集上运行，而无需客户执行任何操作。 预计不会有中断性变更。

## <a name="deprecation"></a>弃用
### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>弃用 HDInsight 3.6 ML 服务群集
HDInsight 3.6 ML 服务群集类型将于 2020 年 12 月 31 日终止支持。 2020 年 12 月 31 日之后，客户将不会创建新的 3.6 ML 服务群集。 现有群集将在没有 Microsoft 支持的情况下按原样运行。 请在[此处](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions)检查 HDInsight 版本的有效期限和群集类型。

### <a name="disabled-vm-sizes"></a>禁用的 VM 大小
自 2020 年 11 月 16 日起，HDInsight 将阻止新客户使用 standand_A8、standand_A9、standand_A10 和 standand_A11 VM 大小创建群集。 过去三个月内使用过这些 VM 大小的现有客户将不会受到影响。 自 2021 年 1 月 9 日起，HDInsight 将阻止所有客户使用 standand_A8、standand_A9、standand_A10 和 standand_A11 VM 大小创建群集。 现有群集将照常运行。 请考虑迁移到 HDInsight 4.0，避免出现潜在的系统/支持中断。

## <a name="behavior-changes"></a>行为更改
此版本没有行为变更。

## <a name="upcoming-changes"></a>即将推出的更改
即将发布的版本中将推出以下变更。

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>能够为 Spark、Hadoop 和 ML 服务选择不同的 Zookeeper 虚拟机大小
目前，HDInsight 不支持为 Spark、Hadoop 和 ML 服务群集类型自定义 Zookeeper 节点大小。 默认情况下为 A2_v2/A2 虚拟机大小（免费提供）。 在即将发布的版本中，可以选择最适合自己方案的 Zookeeper 虚拟机大小。 虚拟机大小不是 A2_v2/A2 的 Zookeeper 节点需要付费。 A2_v2 和 A2 虚拟机仍免费提供。

### <a name="default-cluster-version-will-be-changed-to-40"></a>默认群集版本将更改为 4.0
自 2021 年 2 月起，HDInsight 群集的默认版本将从 3.6 更改为 4.0。 有关可用版本的详细信息，请参阅[可用版本](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions)。 详细了解 [HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hdinsight-version-release) 中的新增功能

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>将于 2021 年 6 月 30 日终止支持 HDInsight 3.6
将终止支持 HDInsight 3.6。 自 2021 年 6 月 30 日起，客户无法创建新的 HDInsight 3.6 群集。 现有群集将在没有 Microsoft 支持的情况下按原样运行。 请考虑迁移到 HDInsight 4.0，避免出现潜在的系统/支持中断。

## <a name="bug-fixes"></a>Bug 修复
HDInsight 会持续改善群集的可靠性和性能。 
### <a name="fix-issue-for-restarting-vms-in-cluster"></a>解决重启群集中的 VM 时出现的问题
解决了重启群集中的 VM 时出现的问题，又可以使用 [PowerShell 或 REST API 重启群集中的节点](https://docs.microsoft.com/azure/hdinsight/cluster-reboot-vm)了。

## <a name="component-version-change"></a>组件版本更改
此发行版未发生组件版本更改。 可以在[此文档](./hdinsight-component-versioning.md)中查找 HDInsight 4.0 和 HDInsight 3.6 的当前组件版本。
