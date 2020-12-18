---
title: 在 Azure Defender for IoT 中查看和管理已加入的传感器
description: 本文介绍如何在 Azure Defender for IoT 中查看和删除已加入的传感器，以及如何在 Azure Defender for IoT 中为已加入的传感器下载新的激活文件。
author: rkarlin
ms.author: rkarlin
ms.date: 10/10/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 00bd0250e00b8aaed6154938a9d1662cb467d716
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105417"
---
# <a name="view-and-manage-onboarded-sensors"></a>查看和管理已加入的传感器

本文介绍如何查看和删除已加入的传感器，以及如何为已加入的传感器下载新的激活文件。

## <a name="update-sensor-management-mode"></a>更新传感器管理模式

你可能想要更新传感器的管理模式。 执行此操作时，需要从传感器管理页中删除当前传感器，并上传新的激活文件。

- 在云托管模式（而不是本地托管模式）下工作：使用云托管传感器的激活文件更新本地托管传感器的激活文件。 重新激活后，传感器检测会显示在传感器和 Azure Defender for IoT 门户中。 成功上传该重新激活文件后，新检测到的警报信息会发送到 Azure。

- 在本地托管模式（而不是云托管模式）下工作：使用本地托管传感器的激活文件更新云托管传感器的激活文件。 重新激活后，传感器检测信息只显示在传感器中。

- 将传感器关联到新的 IoT 中心：你可能想要将传感器与新的 IoT 中心相关联。 为此，请重新注册该传感器，然后上传新的激活文件。

若要重新激活传感器，请执行以下操作：

1. 导航到 Azure Defender for IoT 的“传感器管理”页。

2. 选择要为其上传新的激活文件的传感器。

3. 删除它。

4. 请在新模式下或使用新的 IoT 中心从“加入”页面中再次加入该传感器。

5. 从“下载激活文件”页面中下载激活文件。

6. 登录到 Azure Defender for IoT 传感器控制台。

7. 在传感器控制台中，选择“系统设置”，然后选择“重新激活”。

   ![“重新激活”视图的屏幕截图](media/updates/image14.png)

8. 选择“上传”并选择已保存的文件。

9. 选择“激活”  。
 
## <a name="delete-sensors"></a>删除传感器

如果删除云托管的传感器，则不会将信息发送到 IoT 中心。

当你不再使用本地托管的传感器时，请将其删除。

若要删除传感器，请执行以下操作：

1. 导航到 Azure Defender for IoT 的“传感器管理”页。

2. 选择要删除的传感器。

3. 选择“删除传感器”。

## <a name="next-steps"></a>后续步骤

若要了解有关配置选项的详细信息，请继续阅读有关模块配置的操作指南。
> [!div class="nextstepaction"]
> [模块配置操作指南](./how-to-agent-configuration.md)
