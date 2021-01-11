---
title: CLI：将应用连接到 SQL 数据库
description: 了解如何使用 Azure CLI 自动部署和管理应用服务应用。 此示例演示如何将应用连接到 SQL 数据库。
author: msangapu-msft
tags: azure-service-management
ms.assetid: 7c2efdd0-f553-4038-a77a-e953021b3f77
ms.devlang: azurecli
ms.topic: sample
origin.date: 12/11/2017
ms.date: 12/21/2020
ms.author: v-tawe
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 12b5c38498338ad47b3e464653631933bfea27ba
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98022941"
---
# <a name="connect-an-app-service-app-to-sql-database-using-cli"></a>使用 CLI 将应用服务应用连接到 SQL 数据库

该示例脚本会创建一个应用服务应用，并在 Azure SQL 数据库中创建一个数据库。 然后，它使用应用设置将该数据库与该应用相链接。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

如果选择在本地安装并使用 CLI，需要 Azure CLI 2.0 版或更高版本。 要查找版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI](https://docs.azure.cn/cli/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

```azurecli
#/bin/bash

# Variables
appName="webappwithSQL$RANDOM"
serverName="webappwithsql$RANDOM"
location="ChinaNorth"
startip="0.0.0.0"
endip="0.0.0.0"
username="<replace-with-username>"
sqlServerPassword="<replace-with-password>"

# Create a resource group 
az group create --name myResourceGroup --location $location

# Create an App Service Plan
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --location $location

# Create a Web App
az webapp create --name $appName --plan myAppServicePlan --resource-group myResourceGroup

# Create a SQL Database server
az sql server create --name $serverName --resource-group myResourceGroup --location $location --admin-user $username --admin-password $sqlServerPassword

# Configure firewall for Azure access
az sql server firewall-rule create --server $serverName --resource-group myResourceGroup \
--name AllowYourIp --start-ip-address $startip --end-ip-address $endip

# Create a database called 'MySampleDatabase' on server
az sql db create --server $serverName --resource-group myResourceGroup --name MySampleDatabase \
--service-objective S0

# Get connection string for the database
connstring=$(az sql db show-connection-string --name MySampleDatabase --server $serverName \
--client ado.net --output tsv)

# Add credentials to connection string
connstring=${connstring//<username>/$username}
connstring=${connstring//<password>/$sqlServerPassword}

# Assign the connection string to an app setting in the web app
az webapp config appsettings set --name $appName --resource-group myResourceGroup \
--settings "SQLSRV_CONNSTR=$connstring" 
```

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、应用服务应用、SQL 数据库和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [`az group create`](https://docs.azure.cn/cli/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [`az appservice plan create`](https://docs.azure.cn/cli/appservice/plan#az_appservice_plan_create) | 创建应用服务计划。 |
| [`az webapp create`](https://docs.azure.cn/cli/webapp#az_webapp_create) | 创建应用服务应用。 |
| [`az sql server create`](https://docs.azure.cn/cli/sql/server#az_sql_server_create) | 创建服务器。  |
| [`az sql db create`](https://docs.azure.cn/cli/sql/db#az_sql_db_create) | 创建新数据库。 |
| [`az sql db show-connection-string`](https://docs.azure.cn/cli/sql/db#az_sql_db_show_connection_string) | 生成数据库的连接字符串。 |
| [`az webapp config appsettings set`](https://docs.azure.cn/cli/webapp/config/appsettings#az_webapp_config_appsettings_set) | 创建或更新应用服务应用的应用设置。 应用设置将作为应用的环境变量公开。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/cli)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
