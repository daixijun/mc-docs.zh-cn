---
title: Azure CLI 脚本示例 - 创建 Azure 应用配置存储区
titleSuffix: Azure App Configuration
description: 使用 Azure CLI 脚本示例创建 Azure 应用配置存储区。 请参阅参考文章链接，了解脚本中所用的命令。
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: sample
ms.date: 12/14/2020
ms.author: lcozzens
ms.custom: devx-track-azurecli
ms.openlocfilehash: ebecc11cd63c6a4f5e802f4750a04f688b8b9476
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105175"
---
# <a name="create-an-azure-app-configuration-store"></a>创建 Azure 应用配置存储区

此示例脚本在新资源组中创建 Azure 应用配置的新实例。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0 或更高版本。

<!--If using Azure local Shell, the latest version is already installed.-->

## <a name="sample-script"></a>示例脚本

```azurecli
#!/bin/bash

appConfigName=myTestAppConfigStore
#resource name must be lowercase
myAppConfigStoreName=${appConfigName,,}
myResourceGroupName=$appConfigName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location chinaeast2

# Create the Azure AppConfig Service resource and query the hostName
appConfigHostname=$(az appconfig create \
  --name $myAppConfigStoreName \
  --location chinaeast2 \
  --resource-group $myResourceGroupName \
  --query endpoint \
  --sku free \
  -o tsv
  )

# Get the AppConfig connection string 
appConfigConnectionString=$(az appconfig credential list \
--resource-group $myResourceGroupName \
--name $myAppConfigStoreName \
--query "[?name=='Secondary Read Only'] .connectionString" -o tsv)

# Echo the connection string for use in your application
echo "$appConfigConnectionString"
```

记下为新资源组生成的实际名称。 如果要删除所有组资源，将使用该资源组名称。

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建新资源组和应用程序配置存储区。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az appconfig create](/cli/appconfig#az-appconfig-create) | 创建应用程序配置存储区资源。 |
| [az appconfig credential list](/cli/appconfig/credential#az-appconfig-credential-list) | 列出应用配置存储的访问密钥。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/cli)。

可在 [Azure 应用程序配置 CLI 示例](../cli-samples.md)中查找其他应用程序配置 CLI 脚本示例。
