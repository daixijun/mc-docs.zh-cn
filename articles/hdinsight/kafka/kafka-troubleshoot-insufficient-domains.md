---
title: Azure HDInsight 的“区域中容错域不足”错误
description: 由于 Azure HDInsight 的区域中容错域不足，群集创建失败
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/09/2019
ms.openlocfilehash: c1c247db6a8897c91166faf23e6b59e5ba262a70
ms.sourcegitcommit: 5f07189f06a559d5617771e586d129c10276539e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94551876"
---
# <a name="scenario-cluster-creation-failed-due-to-not-sufficient-fault-domains-in-region-in-azure-hdinsight"></a>方案：由于 Azure HDInsight 的`not sufficient fault domains in region`，群集创建失败

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

尝试创建 Apache Kafka 群集时收到类似 `not sufficient fault domains in region` 的错误消息。

## <a name="cause"></a>原因

容错域是 Azure 数据中心基础硬件的逻辑分组。 每个容错域共享公用电源和网络交换机。 在 HDInsight 群集中实现节点的虚拟机和托管磁盘跨这些容错域分布。 此体系结构可限制物理硬件故障造成的潜在影响。

每个 Azure 区域都有特定数量的容错域。 有关域的列表及其包含的容错域的数量，请参阅有关[可用性集](../../virtual-machines/manage-availability.md)的文档。

在 HDInsight 中，需要在至少具有三个容错域的区域中预配 Kafka 群集。

## <a name="resolution"></a>解决方法

如果要创建群集的区域没有足够的容错域，请与产品团队联系，以便即使没有三个容错域也可以预配群集。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道以获取更多支持：

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.cn/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”  ，或打开“帮助 + 支持”  中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
