---
title: CLI：从 Docker 创建 ASP.NET Core 应用
description: 了解如何使用 Azure CLI 自动部署和管理应用服务应用。 此示例演示了如何从 Docker 中心创建 ASP.NET Core 应用。
author: msangapu-msft
tags: azure-service-management
ms.assetid: 3a2d1983-ff7b-476a-ac44-49ec2aabb31a
ms.devlang: azurecli
ms.topic: sample
origin,date: 12/11/2017
ms.date: 12/28/2020
ms.author: msangapu
ms.custom: devx-track-dotnet, mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 9a374e07e696e1bcd3ccb295221776a35fda3d66
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98023933"
---
# <a name="create-an-aspnet-core-app-in-a-docker-container-from-docker-hub-using-azure-cli"></a>使用 Azure CLI 从 Docker 中心在 Docker 容器中创建 ASP.NET Core 应用

此示例脚本将创建一个资源组、一个 Linux 应用服务计划和一个应用。 然后，它将使用 Docker 容器部署 ASP.NET Core 应用程序。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0 或更高版本。

## <a name="sample-script"></a>示例脚本

```azureccli
#/bin/bash

# Variables
appName="AppServiceLinuxDocker$random"
location="ChinaEast2"
dockerHubContainerPath="<replace-with-docker-container-path>" #format: <username>/<container-or-image>:<tag>

# Create a Resource Group
az group create --name myResourceGroup --location $location

# Create an App Service Plan
az appservice plan create --name AppServiceLinuxDockerPlan --resource-group myResourceGroup --location $location --is-linux --sku S1

# Create a Web App
az webapp create --name $appName --plan AppServiceLinuxDockerPlan --resource-group myResourceGroup

# Configure Web App with a Custom Docker Container from Docker Hub
az webapp config container set --docker-custom-image-name $dockerHubContainerPath --name $appName --resource-group myResourceGroup

# Copy the result of the following command into a browser to see the web app.
echo http://$appName.chinacloudsites.cn
```

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、应用服务应用和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [`az group create`](https://docs.azure.cn/cli/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [`az appservice plan create`](https://docs.azure.cn/cli/appservice/plan#az_appservice_plan_create) | 创建应用服务计划。 |
| [`az webapp create`](https://docs.azure.cn/cli/webapp#az_webapp_create) | 创建应用服务应用。 |
| [`az webapp config container set`](https://docs.azure.cn/cli/webapp/config/container#az_webapp_config_container_set) | 为应用服务应用设置 Docker 容器。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/cli)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
