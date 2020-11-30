---
title: include 文件
description: include 文件
services: virtual-machines
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 01/28/2020
ms.date: 07/06/2020
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: cd458745bff38af9bc75456a905174b5e4b5a044
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301129"
---
<!--Verify Successfully-->
## <a name="list-information"></a>列出信息

使用 [az sig list](https://docs.microsoft.com/cli/azure/sig#az-sig-list) 获取有关可用映像库的位置、状态和其他信息。

```azurecli 
az sig list -o table
```

使用 [az sig image-definition list](https://docs.microsoft.com/cli/azure/sig/image-definition#az-sig-image-definition-list) 列出库中的映像定义，包括有关 OS 类型和状态的信息。

```azurecli 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

使用 [az sig image-version list](https://docs.microsoft.com/cli/azure/sig/image-version#az-sig-image-version-list) 列出库中的共享映像版本。

```azurecli
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

使用 [az sig image-version show](https://docs.microsoft.com/cli/azure/sig/image-version#az-sig-image-version-show) 获取映像版本的 ID。

```azurecli
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```

<!-- Update_Description: update meta properties, wording update, update link -->