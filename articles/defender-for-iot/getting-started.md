---
title: 部署选项
description: 开始了解 Defender for IoT 功能和服务的基本工作流。
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
ms.openlocfilehash: 23f1c0afcd91990e93a3f59c1ab1a6dddc749ed7
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105566"
---
# <a name="getting-started-with-azure-defender-for-iot"></a>Azure Defender for IoT 入门

本文介绍了运行 Azure Defender for IoT 所需的部署和载入流程。 还需要执行其他步骤。 建议了解这些步骤并熟悉随附文档中的信息。

完成所有步骤后，Azure Defender for IoT 传感器将监视你的网络。 根据设置解决方案的方式，还可以将检测发送到本地管理控制台或 IoT 中心。

完成以下步骤可启动并运行 Azure Defender for IoT。

## <a name="1-set-up-azure"></a>1.设置 Azure

- 设置 Azure 帐户。 有关详细信息，请参阅[创建 Azure 帐户](https://docs.microsoft.com/learn/modules/create-an-azure-account/)。

- 防火墙或代理：如果你的防火墙或类似干预网络设备配置为允许特定连接，请验证是否已将 *.azure-devices.net:443 向防火墙或代理开放。 如果不支持通配符，或者需要更多控制，则应在防火墙或代理中打开特定的 IoT 中心 FQDN。 有关详细信息，请参阅[参考 - IoT 中心终结点](/iot-hub/iot-hub-devguide-endpoints)。

## <a name="2-deploy-hardware-software-and-onboard-to-sensor"></a>2.向传感器部署硬件、软件和载入

- 购买传感器硬件并安装软件。 请按照此处概述的步骤进行操作，有关详细信息，请参阅本文和 [Defender for IoT 硬件指南](https://aka.ms/AzureDefenderforIoTBareMetalAppliance)[安装指南](https://aka.ms/AzureDefenderforIoTInstallSensorISO)。

  - 安装传感器后，用安全的方式记录传感器登录凭据。 将激活文件上传到传感器需要这些凭据。

  - 如果使用本地管理的传感器，请以安全的方式记录传感器的 IP 地址或在安装中定义的传感器名称。 在 Defender for IoT 门户注册传感器的过程中，在创建传感器名称时可能需要使用此名称。 可以稍后使用它们以确保更轻松地跟踪 Azure Defender for IoT 门户中的注册名称和传感器控制台中显示的已部署传感器的 IP 地址，并确保这两者之间的命名一致。

- 向 Defender for IoT 门户注册该传感器，并下载传感器激活文件。

- 将激活文件上传到传感器。

## <a name="3-perform-network-setup-for-sensor-monitoring-and-management"></a>3.执行网络设置以用于监视和管理传感器

- 将传感器连接到网络。 具体步骤请参阅[网络设置指南](https://aka.ms/AzureDefenderForIoTNetworkSetup)。

## <a name="4-start-discovering-your-network"></a>4.开始发现网络

- 在传感器控制台中调整系统设置。

- 将传感器连接到本地管理控制台。

有关详细信息，请参阅 [Azure Defender for IoT 传感器用户指南](https://aka.ms/AzureDefenderforIoTUserGuide)和[ Defender for IoT 本地管理控制台用户指南](https://aka.ms/DefenderForIoTManagementConsole)。

## <a name="5-populate-azure-sentinel-with-alert-information"></a>5.用警报信息填充 Azure Sentinel

- 若要将警报信息发送到 Azure Sentinel，请配置 Azure Sentinel：[将数据从 Defender for IoT 连接到 Azure Sentinel](how-to-configure-with-sentinel.md)。
 

## <a name="next-steps"></a>后续步骤

- 启用 [Defender for IoT](quickstart-onboard-iot-hub.md)
- 配置[解决方案](quickstart-configure-your-solution.md)
- [创建安全模块](quickstart-create-security-twin.md)
- 配置[自定义警报](quickstart-create-custom-alerts.md)
- [部署安全代理](how-to-deploy-agent.md)
