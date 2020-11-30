---
title: include 文件
description: include 文件
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
origin.date: 02/02/2018
ms.date: 02/02/2018
ms.author: v-tawe
ms.custom: include file
ms.openlocfilehash: b05353b4d78fdee1658a43ccddc3b123e541fc25
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301053"
---
[!INCLUDE [resource group intro text](resource-group.md)]

在 Azure CLI 中，使用 [`az group create`](/cli/group) 命令创建资源组。 以下示例在“中国北部”位置创建名为“myResourceGroup”的资源组。 要查看“免费”层中应用服务支持的所有位置，请运行 [`az appservice list-locations --sku FREE`](/cli/appservice) 命令。

```azurecli
az group create --name myResourceGroup --location "China North"
```

通常在附近的区域中创建资源组和资源。 

此命令完成后，JSON 输出会显示资源组属性。