---
title: 删除 Azure Cache for Redis - Azure CLI
description: 此 Azure CLI 代码示例演示如何使用命令 az redis delete 删除 Azure Cache for Redis 实例。
author: yegu-ms
ms.author: v-junlch
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 40347d6868b1dedbf5159ba24caf3d5a50ce6fbe
ms.sourcegitcommit: a1f565fd202c1b9fd8c74f814baa499bbb4ed4a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96507290"
---
# <a name="delete-an-azure-cache-for-redis"></a>删除 Azure Redis 缓存

在本方案中，了解如何删除 Azure Redis 缓存。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>示例脚本

```azurecli
#/bin/bash

# Delete a Redis Cache named contosoCache from the Resource Group contosoGroup
az redis delete --name contosoCache --resource-group contosoGroup
```

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令删除 Azure Redis 缓存实例。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az redis 删除](/cli/redis) | 删除 Azure Redis 缓存实例。 |


## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli)。

可以在 [Azure Redis 缓存文档](../cli-samples.md)中找到其他 Azure Redis 缓存 CLI 脚本示例。

