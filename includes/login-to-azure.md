---
author: rockboyfor
ms.service: cosmos-db
ms.topic: include
origin.date: 11/25/2018
ms.date: 03/18/2019
ms.author: v-yeche
ms.openlocfilehash: de545ab123ec23982b80bbe9e25afddd6b96ff88
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301063"
---
## <a name="sign-in-to-azure"></a>登录 Azure

将使用 Azure CLI 创建在 Azure 中托管应用所需的资源。 若要在本地运行 Azure CLI 命令，请使用 [az login](https://docs.azure.cn/zh-cn/cli/#az-login) 命令登录到 Azure 订阅，并按屏幕上的说明进行操作。

<!--Not Available on If you run Azure CLI commands in Azure CLI, you're already signed in.-->

```azurecli
az login
```

> [!NOTE]
> 请先运行 `az cloud set -n AzureChinaCloud` 更改云环境，然后才能在 Azure 中国中使用 Azure CLI。 如果要切换回全局 Azure，请再次运行 `az cloud set -n AzureCloud`。

<!--Update_Description: wording update-->