---
title: Azure CLI 脚本示例 - 将应用服务与应用程序网关集成 | Microsoft Docs
description: Azure CLI 脚本示例 - 将应用服务与应用程序网关集成
services: appservice
documentationcenter: appservice
author: madsd
manager: ccompy
editor: ''
tags: azure-service-management
ms.assetid: 6c16d6f8-3c08-4a59-858e-684a2ceccb5f
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
origin.date: 12/09/2019
ms.date: 12/21/2020
ms.author: v-tawe
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: fe5fab490b7e2383b5de3b7af4acee38e03f8dd5
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98022929"
---
# <a name="integrate-app-service-with-application-gateway-using-cli"></a>使用 CLI 将应用服务与应用程序网关集成

此示例脚本将创建一个 Azure 应用服务 Web 应用、Azure 虚拟网络和应用程序网关。 然后，它将 Web 应用的流量限制为只能源自应用程序网关子网。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0 或更高版本。

<!-- If using Azure Cloud Shell, the latest version is already installed.-->

## <a name="sample-script"></a>示例脚本

```azurecli
#/bin/bash

# This script requires minimum Azure CLI version 2.0.74

# Variables
resourceGroupName="yourResourceGroup"
appName="webappwithgateway$RANDOM"
location="chinaeast"

# Create a Resource Group 
az group create --name $resourceGroupName --location $location

# Create network resources
az network vnet create \
    --resource-group $resourceGroupName \
    --name myVNet \
    --location $location \
    --address-prefix 10.0.0.0/16 \
    --subnet-name myAGSubnet \
    --subnet-prefix 10.0.1.0/24

az network public-ip create \
    --resource-group $resourceGroupName --location $location \
    --name myAGPublicIPAddress --dns-name $appName --sku Standard

# Create an App Service Plan
az appservice plan create --resource-group $resourceGroupName \
    --name myAppServicePlan --location $location --sku S1

# Create a Web App
az webapp create --resource-group $resourceGroupName \
    --name $appName --plan myAppServicePlan

appFqdn=$(az webapp show --name $appName --resource-group $resourceGroupName --query defaultHostName -o tsv)

# Create an Application Gateway
az network application-gateway create \
    --resource-group $resourceGroupName \
    --name myAppGateway \
    --location $location \
    --vnet-name myVNet \
    --subnet myAGsubnet \
    --min-capacity 2 \
    --sku Standard_v2 \
    --http-settings-cookie-based-affinity Disabled \
    --frontend-port 80 \
    --http-settings-port 80 \
    --http-settings-protocol Http \
    --public-ip-address myAGPublicIPAddress \
    --servers $appFqdn

az network application-gateway http-settings update \
    --resource-group $resourceGroupName --gateway-name myAppGateway \
    --name appGatewayBackendHttpSettings \
    --host-name-from-backend-pool

# Apply Access Restriction to Web App
az webapp config access-restriction add \
    --resource-group $resourceGroupName --name $appName \
    --priority 200 --rule-name gateway-access \
    --subnet myAGSubnet --vnet-name myVNet

# Get the App Gateway Fqdn
az network public-ip show \
    --resource-group $resourceGroupName \
    --name myAGPublicIPAddress \
    --query {AppGatewayFqdn:dnsSettings.fqdn} \
    --output table
```

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、应用服务应用、Cosmos DB 和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [`az group create`](https://docs.azure.cn/cli/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [`az network vnet create`](https://docs.azure.cn/cli/network/vnet#az_network_vnet_create) | 创建虚拟网络。 |
| [`az network public-ip create`](https://docs.azure.cn/cli/network/public-ip#az_network_public_ip_create) | 创建公共 IP 地址。 |
| [`az network public-ip show`](https://docs.azure.cn/cli/network/public-ip#az_network_public_ip_show) | 显示公共 IP 地址的详细信息。 |
| [`az appservice plan create`](https://docs.azure.cn/cli/appservice/plan#az_appservice_plan_create) | 创建应用服务计划。 |
| [`az webapp create`](https://docs.azure.cn/cli/webapp#az_webapp_create) | 创建应用服务 Web 应用。 |
| [`az webapp show`](https://docs.azure.cn/cli/webapp#az_webapp_show) | 显示应用服务 Web 应用的详细信息。 |
| [`az webapp config access-restriction add`](https://docs.azure.cn/cli/webapp/config/access-restriction#az_webapp_config_access_restriction_add) | 将访问限制添加到应用服务 Web 应用。 |
| [`az network application-gateway create`](https://docs.azure.cn/cli/network/application-gateway#az_network_application_gateway_create) | 创建应用程序网关。 |
| [`az network application-gateway http-settings update`](https://docs.azure.cn/cli/network/application-gateway/http-settings#az_network_application_gateway_http_settings_update) | 更新应用程序网关 HTTP 设置。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/cli)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
