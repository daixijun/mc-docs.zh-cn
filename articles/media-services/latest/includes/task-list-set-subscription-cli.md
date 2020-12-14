---
author: WenJason
ms.service: media-services
ms.topic: include
origin.date: 08/17/2020
ms.date: 12/14/2020
ms.author: v-jay
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: aadc2d9cc9cab077d1527a9612b9c4b4cb043809
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97004493"
---
<!-- List and set subscriptions -->

1. 使用 [az account list](/cli/account#az-account-list) 命令获取订阅列表。

    ```
    az account list --output table
    ```

2. 结合你要切换到的订阅 ID 或名称使用 `az account set`。

    ```
    az account set --subscription "My Demos"
    ```
