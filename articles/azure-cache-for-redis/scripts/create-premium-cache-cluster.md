---
title: 通过群集创建高级 Azure Cache for Redis - Azure CLI
description: 此 Azure CLI 代码示例演示如何创建启用了群集的 6 GB 高级层 Azure Cache for Redis 和两个分片。
author: yegu-ms
ms.author: v-junlch
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: a3979d691b19c41b909cf2cd30c218d859f27172
ms.sourcegitcommit: a1f565fd202c1b9fd8c74f814baa499bbb4ed4a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96507188"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>通过群集创建高级 Azure Redis 缓存

在此方案中，了解如何通过启用的群集与两个分片创建 6 GB 高级层 Azure Redis 缓存。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>示例脚本

```azurecli
#/bin/bash

# Creates a Resource Group named contosoGroup, and creates a Premium Redis Cache with clustering in that group named contosoCache

# Create a Resource Group 
az group create --name contosoGroup --location chinanorth

# Create a Premium P1 (6 GB) Redis Cache with clustering enabled and 2 shards (for a total of 12 GB)
az redis create --name contosoCache --resource-group contosoGroup --location chinanorth --vm-size P1 --sku Premium --shard-count 2

```

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组并通过启用群集创建高级层 Azure Redis 缓存。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az group create](/cli/group) | 创建用于存储所有资源的资源组。 |
| [az redis create](/cli/redis) | 创建 Azure Redis 缓存实例。 |


## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli)。

可以在 [Azure Redis 缓存文档](../cli-samples.md)中找到其他 Azure Redis 缓存 CLI 脚本示例。

