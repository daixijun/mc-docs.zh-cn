---
title: CLI：部署到过渡槽
description: 了解如何使用 Azure CLI 自动部署和管理应用服务应用。 此示例演示如何将代码部署到过渡槽。
author: msangapu-msft
tags: azure-service-management
ms.assetid: 2b995dcd-e471-4355-9fda-00babcdb156e
ms.devlang: azurecli
ms.topic: sample
origin.date: 12/11/2017
ms.date: 12/21/2020
ms.author: v-tawe
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: ec30afbf35b1513cb988dc1339d95db0ccac303b
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98022931"
---
# <a name="create-an-app-service-app-and-deploy-code-to-a-staging-environment-using-azure-cli"></a>使用 Azure CLI 创建应用服务应用并将代码部署到过渡环境

此示例脚本使用名为“过渡”的附加部署槽位在应用服务中创建应用，并将示例应用部署到“过渡”槽。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0 或更高版本。

<!-- If using Azure Cloud Shell, the latest version is already installed.-->

## <a name="sample-script"></a>示例脚本

```azurecli
#!/bin/bash

# Replace the following URL with a public GitHub repo URL
gitrepo=https://github.com/Azure-Samples/php-docs-hello-world
webappname=mywebapp$RANDOM

# Create a resource group.
az group create --location chinanorth --name myResourceGroup

# Create an App Service plan in STANDARD tier (minimum required by deployment slots).
az appservice plan create --name $webappname --resource-group myResourceGroup --sku S1

# Create a web app.
az webapp create --name $webappname --resource-group myResourceGroup \
--plan $webappname

#Create a deployment slot with the name "staging".
az webapp deployment slot create --name $webappname --resource-group myResourceGroup \
--slot staging

# Deploy sample code to "staging" slot from GitHub.
az webapp deployment source config --name $webappname --resource-group myResourceGroup \
--slot staging --repo-url $gitrepo --branch master --manual-integration

# Copy the result of the following command into a browser to see the staging slot.
echo http://$webappname-staging.chinacloudsites.cn

# Swap the verified/warmed up staging slot into production.
az webapp deployment slot swap --name $webappname --resource-group myResourceGroup \
--slot staging

# Copy the result of the following command into a browser to see the web app in the production slot.
echo http://$webappname.chinacloudsites.cn
```

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [`az group create`](https://docs.azure.cn/cli/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [`az appservice plan create`](https://docs.azure.cn/cli/appservice/plan#az_appservice_plan_create) | 创建应用服务计划。 |
| [`az webapp create`](https://docs.azure.cn/cli/webapp#az_webapp_create) | 创建应用服务应用。 |
| [`az webapp deployment slot create`](https://docs.azure.cn/cli/webapp/deployment/slot#az_webapp_deployment_slot_create) | 创建部署槽。 |
| [`az webapp deployment source config`](https://docs.azure.cn/cli/webapp/deployment/source#az_webapp_deployment_source_config) | 将应用服务应用与 Git 或 Mercurial 存储库相关联。 |
| [`az webapp deployment slot swap`](https://docs.azure.cn/cli/webapp/deployment/slot#az_webapp_deployment_slot_swap) | 将指定的部署槽交换到生产环境。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/cli)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
