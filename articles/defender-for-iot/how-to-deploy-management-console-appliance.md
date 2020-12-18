---
title: 在 Azure Defender for IoT 中部署管理控制台
description: 了解如何在 Azure Defender for IoT 中部署管理控制台。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/14/2020
ms.author: rkarlin
ms.openlocfilehash: f5e1fc34686b4ac0e216f5554d065451e48a5b3f
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105416"
---
# <a name="deploy-the-management-console"></a>部署管理控制台
本文介绍如何部署 Azure Defender for IoT 本地管理控制台。

## <a name="the-on-premises-management-console"></a>本地管理控制台

本地管理控制台提供所有网络资产的综合视图，同时提供与各设施相关的关键 IoT 和 OT 风险指标和警报的实时视图。 它紧密集成了 SOC 工作流和 runbook，可轻松确定缓解活动的优先级和整个站点的威胁风险。

- 集中统一 - 通过提供资产管理、风险和漏洞管理以及带事件响应的威胁监视功能的单个统一平台，降低复杂性。

- 聚合和关联 - 显示、聚合和分析从所有站点收集的数据和警报。

- 控制所有传感器 - 在单个位置配置和监视所有传感器。

   ![Azure Defender for IoT站点管理视图](media/updates/image2.png)

## <a name="deploy-the-on-premises-management-console-appliance"></a>部署本地管理控制台设备

此过程需要获取你自己的硬件和安装软件。 解决方案在经认证的设备上运行。 购买经认证的设备时，请参阅 [Azure Defender for IoT hardware specifications guide](https://aka.ms/AzureDefenderforIoTBareMetalAppliance)（Azure Defender for IoT 硬件规范指南）。

有关下载 ISO 映像和安装传感器软件的详细信息，请参阅“Azure Defender for IoT 安装指南”。

**部署本地管理控制台：**

1. 导航到 Microsoft Azure Defender for IoT。

2. 选择“本地管理控制台”。

   ![Azure Defender for IoT 本地管理控制台视图](media/updates/image15.png)

3. 从“选择版本”菜单中选择版本 3.1。

4. 选择“下载”并保存文件。

5. 安装软件后，执行网络设置任务。 请参阅 [Azure Defender IoT 网络设置指南](https://aka.ms/AzureDefenderForIoTNetworkSetup)获取详细信息。

## <a name="next-steps"></a>后续步骤

若要了解有关配置选项的详细信息，请继续阅读有关模块配置的操作指南。
> [!div class="nextstepaction"]
> [模块配置操作指南](./how-to-agent-configuration.md)
