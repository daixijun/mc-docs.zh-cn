---
title: CLI：从本地 Git 存储库进行部署
description: 了解如何使用 Azure CLI 自动部署和管理应用服务应用。 此示例演示如何从本地 Git 存储库部署代码。
author: msangapu-msft
tags: azure-service-management
ms.assetid: 048f98aa-f708-44cb-9b9e-953f67dc6da8
ms.devlang: azurecli
ms.topic: sample
origin.date: 12/11/2017
ms.date: 12/21/2020
ms.author: v-tawe
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 4d3f9302c08ec31537244f88f1ab13edbf267b64
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98022930"
---
# <a name="create-an-app-service-app-and-deploy-code-from-a-local-git-repository-using-azure-cli"></a>使用 Azure CLI 从本地 Git 存储库创建应用服务应用并部署代码

此示例脚本使用其相关资源，在应用服务中创建应用，并在本地 Git 存储库中部署应用代码。


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0 或更高版本。

<!-- If using Azure Cloud Shell, the latest version is already installed.-->

## <a name="sample-script"></a>示例脚本

```azurecli
#!/bin/bash

gitdirectory=<Replace with path to local Git repo>
username=<Replace with desired deployment username>
password=<Replace with desired deployment password>
webappname=mywebapp$RANDOM

# Create a resource group.
az group create --location chinanorth --name myResourceGroup

# Create an App Service plan in FREE tier.
az appservice plan create --name $webappname --resource-group myResourceGroup --sku FREE

# Create a web app.
az webapp create --name $webappname --resource-group myResourceGroup --plan $webappname

# Set the account-level deployment credentials
az webapp deployment user set --user-name $username --password $password

# Configure local Git and get deployment URL
url=$(az webapp deployment source config-local-git --name $webappname \
--resource-group myResourceGroup --query url --output tsv)

# Add the Azure remote to your local Git respository and push your code
cd $gitdirectory
git remote add azure $url
git push azure master

# When prompted for password, use the value of $password that you specified

# Copy the result of the following command into a browser to see the web app.
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
| [`az webapp deployment user set`](https://docs.azure.cn/cli/webapp/deployment/user#az_webapp_deployment_user_set) | 为应用服务设置帐户级别部署凭据。 |
| [`az webapp deployment source config-local-git`](https://docs.azure.cn/cli/webapp/deployment/source#az_webapp_deployment_source_config_local_git) | 为本地 Git 存储库创建源控件配置。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/cli)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
