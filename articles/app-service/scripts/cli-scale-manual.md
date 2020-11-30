---
title: CLI：手动缩放应用
description: 了解如何使用 Azure CLI 自动部署和管理应用服务应用。 此示例演示如何手动缩放应用。
author: msangapu-msft
tags: azure-service-management
ms.assetid: 251d9074-8fff-4121-ad16-9eca9556ac96
ms.devlang: azurecli
ms.topic: sample
origin.date: 12/11/2017
ms.date: 11/30/2020
ms.author: v-tawe
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 12e3e601ecb85b2d50416c0e12237e39870e192f
ms.sourcegitcommit: f1d0f81918b8c6fca25a125c17ddb80c3a7eda7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2020
ms.locfileid: "96306446"
---
# <a name="scale-an-app-service-app-manually-using-azure-cli"></a>使用 Azure CLI 手动缩放应用服务应用

此示例脚本将创建一个资源组、一个应用服务计划和一个应用。 然后，它将应用服务计划从单个实例扩展到多个实例。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0 或更高版本。

<!-- If using Azure Cloud Shell, the latest version is already installed.-->

## <a name="sample-script"></a>示例脚本

```azurecli
#/bin/bash

# Variables
appName="AppServiceManualScale$random"
location="ChinaNorth"

# Create a Resource Group
az group create --name myResourceGroup --location $location

# Create App Service Plans
az appservice plan create --name AppServiceManualScalePlan --resource-group myResourceGroup --location $location --sku B1

# Add a Web App
az webapp create --name $appName --plan AppServiceManualScalePlan --resource-group myResourceGroup

# Scale Web App to 2 Workers
az appservice plan update --number-of-workers 2 --name AppServiceManualScalePlan --resource-group myResourceGroup
```

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、应用服务应用和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [`az group create`](/cli/group?view=azure-cli-latest#az-group-create) | 创建用于存储所有资源的资源组。 |
| [`az appservice plan create`](/cli/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | 创建应用服务计划。 |
| [`az webapp create`](/cli/webapp?view=azure-cli-latest#az-webapp-create) | 创建应用服务应用。 |
| [`az appservice plan update`](/cli/appservice/plan?view=azure-cli-latest#az-appservice-plan-update) | 更新应用服务计划的属性。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/overview?view=azure-cli-lastest)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
