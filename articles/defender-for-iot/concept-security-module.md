---
title: 安全模块和设备孪生
description: 了解安全模块孪生的概念，以及如何在 Defender for IoT 中使用它们。
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
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: 8cc4c641be455967ca4b080affaffcb6ab6fd3be
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105568"
---
# <a name="security-module"></a>安全模块

本文介绍 Defender for IoT 如何使用设备孪生和模块。

## <a name="device-twins"></a>设备孪生

对于在 Azure 中生成的 IoT 解决方案，设备孪生在设备管理和流程自动化方面发挥着关键作用。

适用于 IoT 的 Defender 可与现有的 IoT 设备管理平台完全集成，使你能够管理设备的安全状态，以及利用现有的设备控制功能。 集成是使用 IoT 中心孪生机制实现的。

详细了解 Azure IoT 中心[设备孪生](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)的概念。

## <a name="security-module-twins"></a>安全模块孪生

Defender for IoT 为服务中的每个设备维护一个安全模块孪生。
该安全模块孪生保存与解决方案中每个特定设备的设备安全相关的所有信息。
设备安全属性在一个专用的安全模块孪生中进行维护，以实现更安全的通信并启用需要更少资源的更新和维护。

请参阅[创建安全模块孪生](quickstart-create-security-twin.md)和[配置安全代理](how-to-agent-configuration.md)，了解如何创建、自定义和配置孪生。 请参阅[了解模块孪生](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)，详细了解 IoT 中心模块孪生的概念。

## <a name="see-also"></a>请参阅

- [Defender for IoT 概述](overview.md)
- [部署安全代理](how-to-deploy-agent.md)
- [安全代理身份验证方法](concept-security-agent-authentication-methods.md)
