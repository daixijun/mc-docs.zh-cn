---
title: Azure CLI 脚本示例 - 删除 Azure 应用配置存储区
titleSuffix: Azure App Configuration
description: 使用 Azure CLI 脚本示例删除 Azure 应用配置存储区。 请参阅参考文章链接，了解脚本中所用的命令。
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/14/2020
ms.author: lcozzens
ms.custom: devx-track-azurecli
ms.openlocfilehash: af81ce5f1c804cd9bd4f356de69125cc0306b432
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105173"
---
# <a name="delete-an-azure-app-configuration-store"></a>删除 Azure 应用配置存储区

此示例脚本删除 Azure 应用配置的实例。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0 或更高版本。

<!--If using Azure local Shell, the latest version is already installed.-->

## <a name="sample-script"></a>示例脚本

```azurecli
#/bin/bash

# Delete an App Configuration store named myTestAppConfigStore from the Resource Group myResourceGroup
az appconfig delete --name myTestAppConfigStore --resource-group myResourceGroup
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令删除应用程序配置存储区。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az appconfig delete](/cli/appconfig#az-appconfig-delete) | 删除应用程序配置存储区资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/cli)。

可在 [Azure 应用程序配置 CLI 示例](../cli-samples.md)中找到其他应用程序配置 CLI 脚本示例。
