---
title: Azure RTOS 安全模块基础知识的概念性说明
description: 了解有关 Azure RTOS 安全模块概念和工作流的基础知识。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 1c860ab38a9bf89061fea6360d9658641bea399a
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105613"
---
# <a name="security-module-for-azure-rtos-preview"></a>Azure RTOS 安全模块（预览版）

通过本文可更好地了解 Azure RTOS 安全模块（包括功能和优势），并可以获取指向相关配置和参考资源的链接。 

## <a name="azure-rtos-iot-security-module"></a>Azure RTOS IoT 安全模块

Azure RTOS 安全模块提供了一个全面的 Azure RTOS 设备安全解决方案作为 NetX Duo 产品/服务的一部分。 在 NetX Duo 产品/服务中，Azure RTOS 随附了内置的 Azure IoT 安全模块，并且会在激活后针对实时操作系统设备上的常见威胁提供全面的防范。 

Azure RTOS 安全模块在后台运行，提供无缝的用户体验，同时使用每个客户与 IoT 中心之间的唯一连接发送安全消息。 Azure RTOS 安全模块在默认情况下已启用。  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTO NetX Duo 是一种高级的行业级 TCP/IP 网络堆栈，专门用于深度嵌入的实时应用程序和 IoT 应用程序。 Azure RTO NetX Duo 是一种双 IPv4 和 IPv6 网络堆栈，提供一组丰富的协议，包括安全和云。 请详细了解 [Azure RTO NetX Duo](https://aka.ms/netxduo) 解决方案。

该模块提供以下功能：

- **检测恶意网络活动**
- **基于自定义警报的设备行为基线**
- **改善设备安全机制**

## <a name="security-module-for-azure-rtos-architecture"></a>Azure RTO 安全模块体系结构

Azure RTO 安全模块由 Azure IoT 中间件平台初始化，并使用 IoT 中心客户端向中心发送安全遥测数据。

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Azure IoT 安全模块状态图和信息流":::

Azure RTOS 安全模块使用三个收集器监视以下设备活动和信息：
- 设备网络活动 TCP、UDP 和 ICM
- 系统信息，如 **Threadx** 和 **NetX Duo** 版本
- 检测信号事件

每个收集器都链接到一个优先级组，每个优先级组都有其自己的间隔，其可能的值为“低”、“中”和“高”。 间隔影响收集和发送数据的时间间隔。

每个时间间隔都是可配置的，并且 IoT 连接器可以启用和禁用，以便进一步[自定义你的解决方案](how-to-azure-rtos-security-module.md)。 

## <a name="supported-security-alerts-and-recommendations"></a>支持的安全警报和建议

Azure RTOS 安全模块支持特定的安全警报和建议。 完成初始配置后，请确保为你的服务[查看并自定义相关的警报和建议值](concept-rtos-security-alerts-recommendations.md)。

## <a name="ready-to-begin"></a>准备好开始了吗？

Azure RTO 安全模块可供免费下载，以用于你的 IoT 设备。 每个 Azure 订阅都通过一个 30 天的试用版提供 Defender for IoT 云服务。 [立即下载安全模块](https://github.com/azure-rtos/azure-iot-preview/releases)，让我们开始吧。 

## <a name="next-steps"></a>后续步骤

- 从 Azure RTOS 安全模块[先决条件和设置](quickstart-azure-rtos-security-module.md)入手。
- 详细了解 Azure RTOS 安全模块的[安全警报和建议支持](concept-rtos-security-alerts-recommendations.md)。 
- 使用 Azure RTOS 安全模块的[参考 API](azure-rtos-security-module-api.md)。

