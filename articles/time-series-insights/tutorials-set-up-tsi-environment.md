---
title: 教程：设置 Gen2 环境 - Azure 时序见解 Gen2 | Microsoft Docs
description: 教程：了解如何在 Azure 时序见解 Gen2 中设置环境。
author: deepakpalled
ms.author: v-junlch
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 12/01/2020
ms.custom: seodec18
ms.openlocfilehash: c092f5c5174546899928c09ec27b7495d6ef7add
ms.sourcegitcommit: a1f565fd202c1b9fd8c74f814baa499bbb4ed4a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96507990"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-gen2-environment"></a>教程：设置 Azure 时序见解 Gen2 环境

本教程将引导你完成创建 Azure 时序见解第 2 代环境的过程。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 创建 Azure 时序见解 Gen2 环境。
> * 将 Azure 时序见解 Gen2 环境连接到 IoT 中心。

注册一个 [Azure 订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn/)（如果还没有）。

## <a name="prerequisites"></a>先决条件

* 必须至少具有 Azure 订阅的参与者角色。 有关详细信息，请参阅[使用 Azure 门户添加或删除 Azure 角色分配](../role-based-access-control/role-assignments-portal.md)。


## <a name="create-an-azure-time-series-insights-gen2-environment"></a>创建 Azure 时序见解 Gen2 环境

本部分介绍如何创建 Azure 时序见解 Gen2 环境，并使用 [Azure 门户](https://portal.azure.cn/)将其连接到由 IoT 解决方案加速器创建的 IoT 中心。

1. 使用 Azure 订阅帐户登录到 [Azure 门户](https://portal.azure.cn)。
1. 选择左上角的“+ 创建资源”。
1. 依次选择“物联网”类别、“时序见解”   。

   [![选择时序见解环境资源。](./media/v2-update-provision/tsi-create-new-environment.png)](./media/v2-update-provision/tsi-create-new-environment.png#lightbox)

1. 在“创建时序见解环境”窗格的“基本信息”选项卡上，设置以下参数：

    | 参数 | 操作 |
    | --- | ---|
    | **环境名称** | 输入 Azure 时序见解 Gen2 环境的唯一名称。 |
    | **订阅** | 输入想要在其中创建 Azure 时序见解 Gen2 环境的订阅。 最佳做法是使用与设备模拟器创建的其他 IoT 资源相同的订阅。 |
    | **资源组** | 为 Azure 时序见解 Gen2 环境资源选择现有的资源组或创建新的资源组。 资源组是 Azure 资源的容器。 最佳做法是使用与设备模拟器创建的其他 IoT 资源相同的资源组。 |
    | **位置** | 为 Azure 时序见解 Gen2 环境选择数据中心区域。 为了避免额外的延迟，最好是在设备模拟器创建的 IoT 中心所在的区域中创建 Azure 时序见解 Gen2 环境。 |
    | **层** |  选择 Gen2(L1)。 这是 Azure 时序见解 Gen2 产品的 SKU。 |
    | **时序 ID 属性名称** | 输入属性名称，需包含唯一标识时序实例的值。 以后不能更改在“属性名称”框中作为时序 ID 输入的值。 在本教程中，请输入 iothub-connection-device-id。 若要详细了解包含复合时序 ID 的时序 ID，请阅读[选择时序 ID 的最佳做法](./how-to-select-tsid.md)。 |
    | 存储帐户名称* | 为新存储帐户输入全局唯一名称。|
    | **存储帐户类型** | 为新的存储帐户选择存储类型。 建议选择 StorageV2|
    | **存储帐户复制** | 为新的存储帐户选择存储类型。 根据你的位置选择，你可以选择 LRS、GRS 或 ZRS。 对于本教程，你可选择 LRS|
    | **分层命名空间** |选择 StorageV2 存储类型后，可以选择此选项。 默认禁用此项。 对于本教程，你可将其保留为默认禁用状态|
    |**启用 Warm 存储**|选择“是”启用 Warm 存储。 也可在创建环境后禁用和重新启用此设置。 |
    |**日志保留期（以天为单位）**|选择默认选项 7 天。 |

    [![新的时序见解环境配置。](./media/v2-update-provision/tsi-environment-configuration.png)](./media/v2-update-provision/tsi-environment-configuration.png#lightbox)

1. 在完成时选择“下一步:**事件源”** 。

   [![配置环境的时序 ID。](./media/v2-update-provision/tsi-time-series-id-selection.png)](./media/v2-update-provision/tsi-time-series-id-selection.png#lightbox)

1. 在“事件源”选项卡上，设置以下参数：

   | 参数 | 操作 |
   | --- | --- |
   | **创建事件源?** | 请选择“是”。|
   | **名称** | 输入事件源名称的唯一值。 |
   | **源类型** | 选择“IoT 中心”。 |
   | **选择中心** | 选择“选择现有”。 |
   | **订阅** | 选择曾经用于设备模拟器的订阅。 |
   | **IoT 中心名称** | 选择为设备模拟器创建的 IoT 中心名称。 |
   | **Iot 中心访问策略** | 选择“iothubowner”。 |
   | **IoT 中心使用者组** | 选择“新建”，输入唯一名称，然后选择“+ 添加” 。 在 Azure 时序见解 Gen2 中，使用者组必须是唯一值。 |
   | **时间戳属性** | 此值用于标识传入遥测数据中的 **时间戳** 属性。 在本教程中，请将此框留空。 此模拟器使用 IoT 中心的传入时间戳，Azure 时序见解 Gen2 默认使用该时间戳。 |

1. 选择“查看 + 创建”。

   [![将创建的 IoT 中心配置为事件源。](./media/v2-update-provision/tsi-configure-event-source.png)](./media/v2-update-provision/tsi-configure-event-source.png#lightbox)

1. 选择“创建”。

    [![包含“创建”按钮的“查看 + 创建”页。](./media/v2-update-provision/tsi-environment-confirmation.png)](./media/v2-update-provision/tsi-environment-confirmation.png#lightbox)

    你可以查看部署的状态：

    [![指出部署已完成的通知。](./media/v2-update-provision/tsi-deployment-notification.png)](./media/v2-update-provision/tsi-deployment-notification.png#lightbox)

1. 如果你是 Azure 订阅的所有者，则默认情况下可以访问 Azure 时序见解 Gen2 环境。 验证你是否具有访问权限：

   1. 搜索资源组，然后选择新创建的 Azure 时序见解 Gen2 环境。

      [![选择并查看环境。](./media/v2-update-provision/verify-tsi-resource-in-group.png)](./media/v2-update-provision/verify-tsi-resource-in-group.png#lightbox)

   1. 在“Azure 时序见解 Gen2”页上，选择“数据访问策略”：

      [![验证数据访问策略。](./media/v2-update-provision/tsi-data-access-panel.png)](./media/v2-update-provision/tsi-data-access-panel.png#lightbox)

   1. 验证凭据是否已列出：

      如果未列出你的凭据，则必须通过选择“添加”并搜索自己的凭据来授予自己访问该环境的权限。 若要详细了解如何设置权限，请参阅[授予数据访问权限](./concepts-access-policies.md)。


## <a name="clean-up-resources"></a>清理资源

现在你已完成本教程，请清除已创建的资源：

1. 从 [Azure 门户](https://portal.azure.cn)的左侧菜单中，选择“所有资源”，找到“Azure 时序见解 Gen2”资源组。
1. 通过选择“删除”来删除整个资源组（以及其中包含的所有资源），或者单独删除每个资源。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：  
 
* 创建 Azure 时序见解 Gen2 PAYG 环境。
* 将 Azure 时序见解 Gen2 环境连接到 IoT 中心。

知道如何创建自己的 Azure 时序见解 Gen2 环境后，现在来详细了解 Azure 时序见解 Gen2 的关键概念。

了解 Azure 时序见解 Gen2 引入：

> [!div class="nextstepaction"]
> [Azure 时序见解 Gen2 数据引入概述](./concepts-ingestion-overview.md)

了解 Azure 时序见解 Gen2 存储：

> [!div class="nextstepaction"]
> [Azure 时序见解 Gen2 数据存储](./concepts-storage.md)

详细了解时序模型：

> [!div class="nextstepaction"]
> [Azure 时序见解 Gen2 数据建模](./concepts-model-overview.md)

详细了解如何将环境连接到 Power BI：

> [!div class="nextstepaction"]
> [在 Power BI 中可视化来自 Azure 时序见解 Gen2 的数据](./how-to-connect-power-bi.md)

