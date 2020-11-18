---
title: Azure Stack Hub 上的 IoT 中心概述
description: 了解 Azure Stack Hub 上的 IoT 中心资源提供程序及其与 IoT 中心的 Azure 托管版本的区别。
author: WenJason
ms.author: v-jay
ms.service: azure-stack
ms.topic: how-to
origin.date: 1/6/2020
ms.date: 11/09/2020
ms.openlocfilehash: afb6212f1ca884d95504be4fc60e8aa394c73915
ms.sourcegitcommit: f187b1a355e2efafea30bca70afce49a2460d0c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330780"
---
# <a name="iot-hub-on-azure-stack-hub-overview"></a>Azure Stack Hub 上的 IoT 中心概述

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

可以通过 Azure Stack Hub 上的 IoT 中心创建混合 IoT 解决方案。 IoT 中心是一项托管服务，充当中央消息中心，用于在 IoT 应用程序与所管理的设备之间进行双向通信。 可以使用 Azure Stack Hub 上的 IoT 中心，在 IoT 设备和本地解决方案后端之间建立可靠又安全的通信，生成 IoT 解决方案。 

## <a name="differences-between-azure-iot-hub-and-iot-hub-on-azure-stack-hub"></a>Azure IoT 中心和 Azure Stack Hub 上的 IoT 中心的区别

| 功能 | Azure IoT 中心 | Azure Stack Hub 上的 IoT 中心 |
|-|-|-|
| 缩放 | /iot-hub/iot-hub-scaling | 支持 S2 和 S3 IoT 中心|
| 服务更新 | 自动 | 手动 |

## <a name="iot-hub-throttling"></a>IoT 中心限制

Azure Stack Hub S2 和 S3 SKU 上的 IoT 中心的限制和配额与 Azure 上的 IoT 中心相同。 有关更多详细信息，请参阅 [Azure IoT 中心配额和限制](/iot-hub/iot-hub-devguide-quotas-throttling#quotas-and-throttling)。

## <a name="next-steps"></a>后续步骤

若要准备在 Azure Stack Hub 上安装 IoT 中心，请查看[先决条件](iot-hub-rp-prerequisites.md)文档。

若要详细了解联机和离线 Azure Stack Hub 之间的差异，请查看 [Azure Stack 连接模型](azure-stack-connection-models.md)。
