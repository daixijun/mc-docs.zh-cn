---
title: Azure 媒体服务 v3 可用的云和区域
description: 本文讨论了用于终结点的 URL 和区域的代码。
services: media-services
author: WenJason
manager: digimobile
ms.service: media-services
ms.topic: reference
origin.date: 10/28/2020
ms.date: 11/30/2020
ms.author: v-jay
ms.openlocfilehash: 39cb6d03a94350bc8911ca97b552c83bb138ed23
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301015"
---
# <a name="regional-code-names-and-endpoints"></a>区域代码名称和终结点

### <a name="region-code-name"></a>区域代码名

在命令或请求中使用 location 参数时，需要提供区域代码名称作为 location 值。 若要获取你的帐户所在的并且应当将你的调用路由到的区域的代码名称，可以在 Azure CLI 中运行以下命令行。

```azurecli
az account list-locations
```

运行上面所示的命令行后，会得到所有 Azure 区域的列表。 导航到具有你要查找的 *displayName* 的 Azure 区域，并将其 *name* 值用于 **位置** 参数。

例如，对于 Azure 区域“中国东部”（如下所示），需将“chinaeast”用于 location 参数。

```json
   {
      "displayName": "China East",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/chinaeast",
      "latitude": "31.3209",
      "longitude": "121.5891",
      "name": "chinaeast",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>终结点  

连接到媒体服务帐户时，需要了解以下终结点。

### <a name="azure-china-21vianet"></a>Azure 中国世纪互联

| 服务 | 终结点 |
| ------- | -------- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| 身份验证 | `https://login.chinacloudapi.cn/` |
| 令牌受众 |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>另请参阅

* [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)
* [区域代码名称和终结点](azure-regions-code-names.md)
* [Azure 地域](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>后续步骤

[媒体服务 v3 概述](media-services-overview.md)
