---
title: 创建 Azure Cache for Redis - Azure CLI
description: 此 Azure CLI 代码示例演示如何使用命令 az redis create 创建 Azure Cache for Redis 实例。
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/30/2020
ms.author: v-junlch
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2b5c89f8a1053f8ffb36e7874a6d41dd7307f495
ms.sourcegitcommit: a1f565fd202c1b9fd8c74f814baa499bbb4ed4a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96507189"
---
# <a name="create-an-azure-cache-for-redis"></a>创建 Azure Redis 缓存

本方案介绍如何创建 Azure Redis 缓存。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>示例脚本

```azurecli
#/bin/bash

# Creates a Resource Group named contosoGroup, and creates a Redis Cache in that group named contosoCache

# Create a Resource Group 
az group create --name contosoGroup --location chinanorth

# Create a Basic C0 (256 MB) Redis Cache
az redis create --name contosoCache --resource-group contosoGroup --location chinanorth --sku Basic --vm-size C0

```

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组和 Azure Redis 缓存。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az group create](/cli/group) | 创建用于存储所有资源的资源组。 |
| [az redis create](/cli/redis) | 创建 Azure Redis 缓存实例。 |


## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli)。

可以在 [Azure Redis 缓存文档](../cli-samples.md)中找到其他 Azure Redis 缓存 CLI 脚本示例。

