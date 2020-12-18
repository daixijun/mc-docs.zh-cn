---
title: 组件和先决条件
description: Azure Defender for IoT 服务先决条件入门所需全部内容的详细信息。
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
ms.date: 10/07/2020
ms.author: rkarlin
ms.custom: references_regions
ms.openlocfilehash: e8ff786837b4d50e61e93791b1680b54eb624f54
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105394"
---
# <a name="azure-defender-for-iot-prerequisites"></a>Azure Defender for IoT 先决条件

本文介绍了 Defender for IoT 服务的不同组件以及开始使用的先决条件，并解释了有助于理解该服务的基本概念。

## <a name="minimum-requirements"></a>最低要求

- 用于 IoT 和 OT 设备的无代理监视（基于 CyberX 技术）
    - 支持通过 SPAN 端口进行流量监视的网络交换机
    - 用于 NTA 传感器的硬件设备，有关详细信息，请参阅[认证硬件](https://aka.ms/AzureDefenderforIoTBareMetalAppliance)
    - Azure 订阅参与者角色（仅在加入期间需要用于定义提交的设备）
    - IoT 中心（免费或标准层）参与者角色（用于云连接管理）
- 通过 Azure IoT 中心管理的托管 IoT 设备的安全性
    - IoT 中心（标准层）参与者角色
    - IoT 中心：应启用 Azure Defender for IoT 功能切换
    - 对于设备级别安全模块支持  
        - Defender for IoT 代理支持不断扩充的设备和平台列表，请参阅[支持的平台列表](how-to-deploy-agent.md)


## <a name="supported-service-regions"></a>支持的服务区域

有关详细信息，请参阅 [IoT 中心支持的区域](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)。 


## <a name="next-steps"></a>后续步骤

- 阅读 Azure IoT 安全[概览](overview.md)
- 了解如何[启用服务](quickstart-onboard-iot-hub.md)
- 阅读 [Defender for IoT 常见问题解答](resources-frequently-asked-questions.md)
- 了解如何[理解 Defender for IoT 警报](concept-security-alerts.md)
