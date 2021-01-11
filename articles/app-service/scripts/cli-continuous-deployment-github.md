---
title: CLI：从 GitHub 进行的持续部署
description: 了解如何使用 Azure CLI 自动部署和管理应用服务应用。 此示例演示如何使用 CI/CD 从 GitHub 创建应用。
author: msangapu-msft
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.devlang: azurecli
ms.topic: sample
origin.date: 09/02/2019
ms.date: 12/21/2020
ms.author: v-tawe
ms.custom: mvc, seodec18
ms.openlocfilehash: da9b609d46a928646c58ecc254a6f25eebe08ab8
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98022939"
---
# <a name="create-an-app-service-app-with-continuous-deployment-from-github-using-cli"></a>使用 CLI 从 GitHub 通过持续部署创建应用服务应用

此示例脚本使用其相关资源在应用服务中创建应用，并从 GitHub 存储库设置持续部署。 有关不进行持续部署的 GitHub 部署，请参阅[从 GitHub 创建应用并部署代码](cli-deploy-github.md)。 在此示例中，需要以下项：

* 包含应用程序代码且你对其拥有管理权限的 GitHub 存储库。
* GitHub 帐户的[个人访问令牌(PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

如果选择在本地安装并使用 CLI，需要 Azure CLI 2.0 版或更高版本。 要查找版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI](https://docs.azure.cn/cli/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

```azurecli
#!/bin/bash

gitrepo=<replace-with-URL-of-your-own-GitHub-repo>
token=<replace-with-a-GitHub-access-token>
webappname=mywebapp$RANDOM

# Create a resource group.
az group create --location chinaeast --name myResourceGroup

# Create an App Service plan in `FREE` tier.
az appservice plan create --name $webappname --resource-group myResourceGroup --sku FREE

# Create a web app.
az webapp create --name $webappname --resource-group myResourceGroup --plan $webappname

# Configure continuous deployment from GitHub. 
# --git-token parameter is required only once per Azure account (Azure remembers token).
az webapp deployment source config --name $webappname --resource-group myResourceGroup \
--repo-url $gitrepo --branch master --git-token $token

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
| [`az webapp deployment source config`](https://docs.azure.cn/cli/webapp/deployment/source#az_webapp_deployment_source_config) | 将应用服务应用与 Git 或 Mercurial 存储库相关联。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/cli)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。