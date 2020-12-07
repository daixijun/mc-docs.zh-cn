---
title: 获取 Azure Cache for Redis 的详细信息 - Azure CLI
description: 此 Azure CLI 代码示例演示如何检索 Azure Cache for Redis 实例的详细信息，包括其预配状态。
author: yegu-ms
ms.author: v-junlch
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: a6aca7ce555c437728733a609711c6bf561687a9
ms.sourcegitcommit: a1f565fd202c1b9fd8c74f814baa499bbb4ed4a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96507288"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>获取 Azure Redis 缓存的详细信息

在此方案中，你将了解如何检索 Azure Redis 缓存实例的详细信息，包括其预配状态。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>示例脚本

```azurecli
#/bin/bash

# Retrieve the details for an Azure Redis Cache instance named contosoCache in the Resource Group contosoGroup
# This script shows details such as hostname, ports, and provisioning status
az redis show --name contosoCache --resource-group contosoGroup 
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令检索 Azure Redis 缓存实例的详细信息。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az redis show](/cli/redis) | 检索 Azure Redis 缓存实例的详细信息。 |


## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli)。

可以在 [Azure Redis 缓存文档](../cli-samples.md)中找到其他 Azure Redis 缓存 CLI 脚本示例。

