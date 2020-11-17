---
title: Azure Stack Hub 上的 IoT 中心的概述
description: 了解 Azure Stack Hub 上的 IoT 中心资源提供程序。
author: WenJason
ms.author: v-jay
ms.service: azure-stack
ms.topic: how-to
origin.date: 12/12/2019
ms.date: 11/09/2020
ms.openlocfilehash: dedd6294f04d2ca54dd7a93c3b2d6181e5398db4
ms.sourcegitcommit: f187b1a355e2efafea30bca70afce49a2460d0c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330716"
---
# <a name="overview-of-iot-hub-on-azure-stack-hub"></a>Azure Stack Hub 上的 IoT 中心的概述

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

通过 Azure Stack Hub 上的 IoT 中心，可以创建混合 IoT 解决方案。 IoT 中心是一项托管服务，充当消息中心，用于在 IoT 应用程序与所管理的设备之间进行双向通信。 可以使用 Azure Stack Hub 上的 IoT 中心，在 IoT 设备和本地解决方案后端之间建立可靠又安全的通信，生成 IoT 解决方案。

## <a name="features"></a>功能

| 功能 | Azure 上的 IoT 中心 | Azure Stack Hub 上的 IoT 中心预览版 (S2/S3) |
|-|-|-|
|设备到云的遥测| ✔ | ✔ |
|云到设备的消息传送| ✔ | ✔ |
|每设备标识| ✔ | ✔ |
|消息路由 <sub>1</sub><sub>，4</sub>| ✔ | ✔ |
|HTTP、AMQP、MQTT 协议| ✔ | ✔ |
|多租户| ✔ | ✔ |
|监视和诊断| ✔ | ✔ |
|云到设备的消息传送| ✔ | ✔ |
|设备管理、设备孪生、模块孪生| ✔ | ✔ |
|孪生通知、设备生命周期事件| ✔ | ✔ |
|边缘分层部署| ✔ | 即将推出 |
|管理员门户 <sub>2</sub>| ✘ | ✔ |
|机密轮换 <sub>2</sub>| ✘ | ✔ |
|容量管理 <sub>2</sub>| ✘ | ✔ |
|备份和还原 <sub>3</sub>| ✘ | ✘ |
|DeviceConnected、DeviceDisconnected、ASC <sub>4</sub>| ✔ | ✘ |
|设备模块配置| ✔ | 即将推出 |
|设备流式处理、IoT 即插即用、作业、文件上传 <sub>5</sub>| ✔ | ✘ |
|使用事件网格监视设备连接状态 <sub>4</sub>| ✔ | ✘ |
|故障转移 <sub>6</sub>| ✔ | ✘ |

<sub>1</sub> 仅限于内置终结点、事件中心和存储。 Azure Stack Hub 不提供服务总线。  
<sub>2</sub> 供操作员在 ASH 上管理 IoT 中心。  
<sub>3</sub> 预览版中提供备份。 正式版将支持还原。  
<sub>4</sub> 依赖于 Azure Stack Hub 中不提供的其他服务。  
<sub>5</sub> 在迁移到 Azure Stack Hub 的路线图中。  
<sub>6</sub> 在 Azure Stack Hub 中不适用。  

## <a name="api-available-for-iot-hub-on-azure-stack-hub"></a>Azure Stack Hub 上的 IoT 中心提供的 API

|API|Azure Stack Hub 上的 IoT 中心|
|-|-|
|Apply Configuration On Device| ✔ |
| Configuration Create | ✔ |
| Configuration Delete | ✔ |
| Configuration Read | ✔ |
|Configuration Read Many| ✔ |
|Configuration Service Apply|  ✔ |
|Configuration Update|  ✔ |
|Device Direct Invoke Method|  ✔ |
|GetDeviceAndModuleInScope|  ✔ |
|GetDevicesAndModulesInScope| ✔ |
|Unregister Device| ✔ |
|Get Devices| ✔ |
|Update Module Twin| ✔ |
|D2C Get Twin| ✔ |
|导入设备| ✔ |
|Get Twin| ✔ |
|Unregister Module| ✔ |
|更新设备| ✔ |
|Update Module| ✔ |
|Query Devices| ✔ |
|导出设备| ✔ |
|Back up and Restore – ADM| ✔ |
|Replace Twin| ✔ |
|Back up and Restore – DSS| ✔ |
|D2C Notify DesiredProperties| ✔ |
|D2C Patch ReportedProperties| ✔ |
|Get Module Twin| ✔ |
|Module D2C Get Twin| ✔ |
|获取模块| ✔ |
|Module D2C Notify DesiredProperties| ✔ |
|Module D2C Patch ReportedProperties| ✔ |
|Module Direct Invoke Method| ✔ |
|Update Twin| ✔ |
|Bulk Device Operations| ✔ |
|Device to Cloud Telemetry| ✔ |
|注册设备| ✔ |
|Register Module| ✔ |
|Replace Module Twin| ✔ |
|GenericAuthentication| ✔ |
|Get Device| ✔ |
|Partition Move/Role Change| ✔ |

## <a name="differences-between-iot-hub-on-azure-cloud-and-iot-hub-on-azure-stack"></a>Azure Cloud 上的 IoT 中心和 Azure Stack 上的 IoT 中心的区别

| 方面 | Cloud 上的 IoT 中心 | Stack 上的 IoT 中心 |
|-|-|-|
| 消息消耗 | /iot-hub/iot-hub-devguide-messages-read-builtin |默认情况下，消息将路由到与事件中心兼容的内置面向服务的终结点 (messages/events) 中。 在 Azure 云上，可以通过提供 IoT 中心连接字符串或事件中心连接字符串从终结点访问消息。 但是，在 Azure Stack Hub 上，只支持事件中心连接字符串。 |

## <a name="next-steps"></a>后续步骤

如果 IoT 中心在订阅中不可用，请与管理员协作，[在 Azure Stack Hub 资源提供程序上安装 IoT 中心](../operator/iot-hub-rp-overview.md)。

若要了解如何使用 IoT 中心，请参阅 [Azure IoT 中心文档](/iot-hub/)。

