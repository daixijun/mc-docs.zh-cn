---
title: Azure CLI 脚本 - 下载 Azure Database for PostgreSQL 中的服务器日志
description: 此示例 Azure CLI 脚本演示如何启用和下载 Azure Database for PostgreSQL 的服务器日志。
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
origin.date: 02/28/2018
ms.date: 12/14/2020
ms.openlocfilehash: cd3c0ca4260b39f6455b0f17f7563f22e0794320
ms.sourcegitcommit: a8afac9982deafcf0652c63fe1615ba0ef1877be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96850730"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>使用 Azure CLI 启用和下载 Azure Database for PostgreSQL 服务器的服务器慢查询日志
此示例 CLI 脚本可启用和下载单个 Azure Database for PostgreSQL 服务器的慢查询日志。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.0 或更高版本。 

## <a name="sample-script"></a>示例脚本
在此示例脚本中，编辑突出显示的行，将管理员用户名和密码更新为你自己的。 将 `az monitor` 命令中的 &lt;log_file_name&gt; 替换自己的服务器日志文件名。

```cli
#!/bin/bash

# Create a resource group
az group create \
--name myresourcegroup  \
--location chinaeast2

# Create a PostgreSQL server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure
# Substitute the <server_admin_password> with your own value
az postgres server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location chinaeast2 \
--admin-user myadmin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2 \

# List the configuration options for review
az postgres server configuration list \
--resource-group myresourcegroup  \
--server mydemoserver

# Turn on statement level log
az postgres server configuration set \
--name log_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value all

# Set log_min_duration_statement time to 10 sec
az postgres server configuration set \
--name log_min_duration_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value 10000

# List the available log files and direct to a text file
az postgres server-logs list \
--resource-group myresourcegroup \
--server mydemoserver > log_files_list.txt

# Download log file from Azure 
# Review log_files_list.txt to find the server log file name for the desired timeframe
# Substitute the <log_file_name> with your server log file name
# Creates the postgresql-<date>_000000.log file in the current command line path
az postgres server-logs download \
--name <log_file_name> \
--resource-group myresourcegroup \
--server mydemoserver
```

## <a name="clean-up-deployment"></a>清理部署
运行脚本示例后，请使用以下命令删除资源组以及与其关联的所有资源。 

```bash
#!/bin/bash
az group delete --name myresourcegroup
```

## <a name="script-explanation"></a>脚本说明
此脚本使用下表中列出的命令：

| **命令** | **说明** |
|---|---|
| [az group create](/cli/group) | 创建用于存储所有资源的资源组。 |
| [az postgres server create](/cli/postgres/server) | 创建托管数据库的 PostgreSQL 服务器。 |
| [az postgres server configuration list](/cli/postgres/server/configuration) | 列出服务器的配置值。 |
| [az postgres server configuration set](/cli/postgres/server/configuration) | 更新服务器的配置。 |
| [az postgres server-logs list](/cli/postgres/server-logs) | 列出服务器的日志文件。 |
| [az postgres server-logs download](/cli/postgres/server-logs) | 下载日志文件。 |
| [az group delete](/cli/group) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
- 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli)。
- 尝试其他脚本：[Azure Database for PostgreSQL 的 Azure CLI 示例](../sample-scripts-azure-cli.md)
- [在 Azure 门户中配置和访问服务器日志](../howto-configure-server-logs-in-portal.md)
