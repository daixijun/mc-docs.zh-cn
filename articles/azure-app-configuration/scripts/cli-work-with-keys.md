---
title: Azure CLI 脚本示例 - 在应用配置存储区中使用密钥值
titleSuffix: Azure App Configuration
description: 使用 Azure CLI 脚本在应用配置存储中创建、查看、更新和删除密钥值
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/14/2020
ms.author: lcozzens
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2a47d746f781fbbf7d008dd24883c6a36d401c32
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105030"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>处理 Azure 应用配置存储区中的密钥值

此示例脚本演示如何：
* 创建新的键值对
* 列出所有现有的键值对
* 更新新建的密钥的值
* 删除新的键值对

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0 或更高版本。

<!--If using Azure local Shell, the latest version is already installed.-->

## <a name="sample-script"></a>示例脚本

```azurecli
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"
refKey="KeyVaultReferenceTestKey"
uri="[URL to value stored in Key Vault]"
uri2="[URL to another value stored in Key Vault]"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --key $newKey --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Create a new key-value referencing a value stored in Azure Key Vault
az appconfig kv set --name $appConfigName --key $refKey --content-type "application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8" --value "{\"uri\":\"$uri\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Update Key Vault reference
az appconfig kv set --name $appConfigName --key $refKey --value "{\"uri\":\"$uri2\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# Delete Key Vault reference
az appconfig kv delete --name $appConfigName --key $refKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

下表列出了我们的示例脚本中所用的命令。 

| 命令 | 说明 |
|---|---|
| [az appconfig kv set](/cli/appconfig/kv#az-appconfig-kv-set) | 创建或更新键值对。 |
| [az appconfig kv list](/cli/appconfig/kv#az-appconfig-kv-list) | 列出应用配置存储中的键值对。 |
| [az appconfig kv delete](/cli/appconfig/kv#az-appconfig-kv-delete) | 删除键值对。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/cli)。

可在 [Azure 应用程序配置 CLI 示例](../cli-samples.md)中找到其他应用程序配置 CLI 脚本示例。
