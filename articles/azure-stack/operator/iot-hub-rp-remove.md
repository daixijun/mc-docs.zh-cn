---
title: 如何删除 Azure Stack Hub 上的 IoT 中心
description: 在 Azure Stack Hub 上卸载 IoT 中心的说明
author: WenJason
ms.author: v-jay
ms.service: azure-stack
ms.topic: how-to
origin.date: 1/6/2020
ms.date: 11/09/2020
ms.openlocfilehash: 99c2bb74fc93e7558ec5ba91a5502f94fe21dfb0
ms.sourcegitcommit: f187b1a355e2efafea30bca70afce49a2460d0c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330776"
---
# <a name="how-to-remove-iot-hub-on-azure-stack-hub"></a>如何删除 Azure Stack Hub 上的 IoT 中心

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

本文提供了有关如何在 Azure Stack Hub 上删除 IoT 中心资源提供程序的说明。 此过程大约需要 37 分钟。

> [!WARNING]
> 卸载 IoT 中心后，将删除所有设备和数据。 该操作不可恢复。

## <a name="uninstalling-iot-hub"></a>卸载 IoT 中心

1) 转到市场管理。 IoT 中心将在列表中，并标记为“已安装”。 单击“IoT 中心”。

    [![资源提供程序列表](../operator/media/iot-hub-rp-remove/uninstall1.png)](../operator/media/iot-hub-rp-remove/uninstall1.png#lightbox)

2) 单击“IoT 中心”下的“卸载”，提供资源提供程序名称 microsoft.iothub，并单击它下面的“卸载”按钮   。

    [![卸载 IoT 中心并确认](../operator/media/iot-hub-rp-remove/uninstall2.png)](../operator/media/iot-hub-rp-remove/uninstall2.png#lightbox)

3) 等待卸载完成。 再次尝试安装 IoT 中心之前，请至少等待 10 分钟。

>[!IMPORTANT]
>依赖项（例如 事件中心）将不会被卸载。 如果希望从市场卸载/删除依赖项，需要单独执行操作。

## <a name="next-steps"></a>后续步骤

如需重新安装，请参阅[在连接的 Azure Stack 上安装 IoT 中心资源提供程序](iot-hub-rp-install.md)。
