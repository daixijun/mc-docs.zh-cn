---
title: 为 Defender for IoT 配置 Azure Sentinel（预览版）
description: 介绍如何配置 Azure Sentinel 以从 Defender for IoT 解决方案接收数据。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: a9ab7b633afd290f70b3f0f8f879cf3681fc11ff
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105563"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel-preview"></a>将数据从 Defender for IoT 连接到 Azure Sentinel（预览版）

Azure Sentinel 中的 Azure Defender for IoT 数据连接器目前为公共预览版。 此功能不附带服务级别协议，不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/)。

在本指南中，了解如何将 Defender for IoT 数据连接到 Azure Sentinel。

> [!div class="checklist"]
> * 先决条件
> * 连接设置
> * Log Analytics 警报视图

从 Defender for IoT 连接警报并将其直接流式传输到 Azure Sentinel。

通过更紧密地将 Azure Defender for IoT 与 Azure Sentinel（第一个云原生 SIEM 和第一个具有本机 IoT 和 OT 安全性的 SIEM）集成，Microsoft 提供了一种更简单的方法来实现 IT 和工业网络中的统一安全性。 与 Azure Sentinel 的机器学习相结合时，这种集成使组织可以快速检测到通过跨越 IT 和 OT 边界的多阶段攻击。 此外，通过将 Azure Defender for IoT 与 Azure Sentinel 安全业务流程、自动化和响应 (SOAR) 功能集成，可以使用内置的 OT 优化 playbook 来实现自动响应和预防。 

## <a name="prerequisites"></a>先决条件

- 必须拥有工作区读取和写入权限 。
- 必须在相关 IoT 中心上启用 Defender for IoT 。
- 必须对要连接的 Azure IoT 中心拥有读取和写入权限  。
- 还必须对 Azure IoT 中心资源组拥有读取和写入权限  。


## <a name="connect-to-defender-for-iot"></a>连接到 Defender for IoT

1. 在 Azure Sentinel 中，选择“数据连接器”，然后单击“Defender for IoT”磁贴。
1. 在右窗格底部，单击“打开连接器页”。
1. 在要将其警报和设备警报流式传输到 Azure Sentinel 的每个 IoT 中心订阅旁，单击“连接”。
    - 如果在该中心上未启用 Defender for IoT，你会看到启用警告消息。 单击“启用”链接以启动和启用服务。
1. 可以决定是否希望 Defender for IoT 中的警报在 Azure Sentinel 中自动生成事件。 在“创建事件”下，选择“启用”以使规则可以自动通过生成的警报创建事件 。  此规则可以在“Analytics” > “活动”规则下进行更改或编辑。

> [!NOTE]
>进行连接更改之后，可能需要 10 秒或更长时间才能刷新中心列表。

## <a name="using-log-analytics-for-alert-display"></a>使用 Log Analytics 进行警报显示

若要在 Log Analytics 中使用相关架构显示 Defender for IoT 警报，请执行以下操作：

1. 打开“日志” > “SecurityInsights” > “SecurityAlert”，或搜索“SecurityAlert”。
1. 使用以下 kql 筛选器进行筛选，以便仅查看 Defender for IoT 生成的警报：

```kusto
SecurityAlert | where ProductName == "Defender for IoT"
```

### <a name="service-notes"></a>服务说明

连接 IoT 中心之后，大约 15 分钟后，Azure Sentinel 中会提供中心数据。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Defender for IoT 连接到 Azure Sentinel。 若要了解有关威胁检测和安全数据访问的详细信息，请参阅以下文章：


- 了解如何[访问 IoT 安全性数据](how-to-security-data-access.md)
