---
title: 使用 Azure CLI 创建 IoT 中心 | Microsoft Docs
description: 了解如何使用 Azure CLI 命令创建资源组，然后在该资源组中创建 IoT 中心。 并了解如何删除中心。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
origin.date: 08/23/2018
ms.author: v-yiso
ms.date: 10/08/2018
ms.openlocfilehash: f41c3c25124721c4288b93b234b7e1db928084b6
ms.sourcegitcommit: ac1cb9a6531f2c843002914023757ab3f306dc3e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2020
ms.locfileid: "96747144"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>使用 Azure CLI 创建 IoT 中心

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

本文介绍如何使用 Azure CLI 创建 IoT 中心。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-iot-hub"></a>创建 IoT 中心

使用 Azure CLI 创建资源组，并添加 IoT 中心。

1. 创建 IoT 中心时，必须在资源组中创建它。 使用现有资源组，或运行以下[命令创建资源组][lnk-az-resource-command]：

    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > 上一示例在美国西部位置创建资源组。 可以通过运行以下命令来查看可用位置列表： 
    >
    > ```azurecli
    >az account list-locations -o table
    >```
    >

2. 运行以下[命令，使用 IoT 中心的全局唯一名称在资源组中创建 IoT 中心][lnk-az-iot-command]：
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


上一命令在计费的 S1 定价层中创建 IoT 中心。 有关详细信息，请参阅 [Azure IoT 中心定价][lnk-iot-pricing]。

## <a name="remove-an-iot-hub"></a>删除 IoT 中心

可使用 Azure CLI [删除单个资源][lnk-az-resource-command]（例如 IoT 中心），或删除资源组及其所有资源（包括任何 IoT 中心）。

若要[删除 IoT 中心](/cli/iot/hub#az-iot-hub-delete)，请运行以下命令：

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

若要[删除资源组](/cli/group#az-group-delete)及其所有资源，请运行以下命令：

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 IoT 中心，请参阅以下文章：

* [IoT 中心开发人员指南](iot-hub-devguide.md)
* [使用 Azure 门户管理 IoT 中心](iot-hub-create-through-portal.md)

<!-- Links -->
[lnk-free-trial]: https://www.microsoft.com/china/azure/index.html?fromtype=cn/
[lnk-CLI-install]: https://docs.azure.cn/zh-cn/cli/install-az-cli2?view=azure-cli-lastest
[lnk-login-command]: https://docs.azure.cn/zh-cn/cli/get-started-with-azure-cli?view=azure-cli-lastest
[lnk-az-account-command]: https://docs.azure.cn/zh-cn/cli/account?view=azure-cli-latest
[lnk-az-register-command]: https://docs.azure.cn/zh-cn/cli/provider?view=azure-cli-latest
[lnk-az-addcomponent-command]: https://docs.azure.cn/zh-cn/cli/component?view=azure-cli-latest
[lnk-az-resource-command]: https://docs.azure.cn/zh-cn/cli/resource?view=azure-cli-latest
[lnk-az-iot-command]: https://docs.azure.cn/zh-cn/cli/iot?view=azure-cli-latest
[lnk-iot-pricing]: https://www.azure.cn/pricing/details/iot-hub/
[lnk-devguide]: ./iot-hub-devguide.md
[lnk-portal]: ./iot-hub-create-through-portal.md

<!--Update_Description: update meta data only-->