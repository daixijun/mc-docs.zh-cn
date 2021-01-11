---
title: CLI：从 ACR 创建 ASP.NET Core 应用
description: 了解如何使用 Azure CLI 自动部署和管理应用服务应用。 此示例演示了如何从 ACR 创建 Linux ASP.NET Core 应用。
author: msangapu-msft
tags: azure-service-management
ms.assetid: 3a2d1983-ff7b-476a-ac44-49ec2aabb31a
ms.devlang: azurecli
ms.topic: sample
origin.date: 12/13/2018
ms.date: 12/28/2020
ms.author: msangapu
ms.custom: devx-track-dotnet, mvc, seodec18
ms.openlocfilehash: 9a6d4b001a6df814ea09cc73e64ced197668a46e
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98023934"
---
# <a name="create-an-aspnet-core-app-in-a-docker-container-in-app-service-from-azure-container-registry"></a>在应用服务的 Docker 容器中通过 Azure 容器注册表创建 ASP.NET Core 应用

此示例脚本将创建一个资源组、一个 Linux 应用服务计划和一个应用。 然后，它将使用 Docker 容器从 Azure 容器注册表部署 ASP.NET Core 应用程序。


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

需要 Azure CLI 2.0.52 或更高版本。 要查找版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI](https://docs.azure.cn/cli/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

```azurecli
# Create a resource group.
az group create --name myResourceGroup --location chinaeast2

# Create an Azure Container Registry.
az acr create --name <registry_name> --resource-group myResourceGroup --location chinaeast2 --sku basic --admin-enabled true --query loginServer --output tsv

# Show ACR credentials.
az acr credential show --name <registry_name> --resource-group myResourceGroup --query [username,passwords[?name=='password'].value] --output tsv

# Before continuing, save the ACR credentials and registry URL. You will need this information in the commands below.

# Pull from Docker.
docker login <acr_registry_name>.azurecr.cn -u <registry_user>
docker pull <registry_user/container_name:version>

# Tag Docker image.
docker tag <registry_user/container_name:version> <acr_registry_name>.azurecr.io/<container_name:version>

# Push container image to Azure Container Registry.
docker push <acr_registry_name>.azurecr.io/<container_name:version>

# Create an App Service plan.
az appservice plan create --name AppServiceLinuxDockerPlan --resource-group myResourceGroup --location chinaeast2 --is-linux --sku S1

# Create a web app.
az webapp create --name <app_name> --plan AppServiceLinuxDockerPlan --resource-group myResourceGroup --deployment-container-image-name <acr_registry_name>.azurecr.io/<container_name:version>

# Configure web app with a custom Docker Container from Azure Container Registry.
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-url http://<acr_registry_name>.azurecr.io --docker-registry-server-user <registry_user> --docker-registry-server-password <registry_password>
```

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、应用服务应用和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [`az group create`](https://docs.azure.cn/cli/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [`az appservice plan create`](https://docs.azure.cn/cli/appservice/plan#az_appservice_plan_create) | 创建应用服务计划。 |
| [`az webapp create`](https://docs.azure.cn/cli/webapp#az_webapp_create) | 创建应用服务应用。 |
| [`az webapp config container set`](https://docs.azure.cn/cli/webapp/config/container#az_webapp_config_container_set) | 为应用服务应用设置 Docker 容器。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/cli)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
