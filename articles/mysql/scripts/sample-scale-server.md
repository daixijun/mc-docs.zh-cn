---
title: CLI 脚本 - 缩放服务器 - Azure Database for MySQL
description: 此示例 CLI 脚本在查询指标后用于 MySQL 服务器的 Azure 数据库缩放为不同的性能级别。
author: WenJason
ms.author: v-jay
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
origin.date: 12/02/2019
ms.date: 11/23/2020
ms.openlocfilehash: 2dc5493c5ee4be026830f89e91e774a951711e03
ms.sourcegitcommit: c2c9dc65b886542d220ae17afcb1d1ab0a941932
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "95970817"
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>使用 Azure CLI 监视和缩放用于 MySQL 服务器的 Azure 数据库
此示例 CLI 脚本在查询指标后为单个 Azure Database for MySQL 服务器缩放计算和存储。 计算可以增加或减少。 存储只能增加。

> [!NOTE]
> 将要查看的是 Azure Database for MySQL 的新服务。 若要查看经典 MySQL Database for Azure 的文档，请访问[此页](https://docs.azure.cn/zh-cn/mysql-database-on-azure/)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.0 或更高版本。 

## <a name="sample-script"></a>示例脚本
使用你的订阅 ID 更新脚本。

```cli
#!/bin/bash

# Set up variables
RESOURCE_GROUP="myresourcegroup"
SERVER_NAME="mydemoserver-$RANDOM"
PASSWORD=$(head /dev/urandom | tr -dc A-Za-z0-9 | head -c 12) 
echo password $PASSWORD # returns the generated password
LOCATION="chinaeast2"
ADMIN_USER="myadmin"
SUBSCRIPTION_ID="" # enter your subscription ID

# Create a resource group
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION

# Create a MySQL server in the resource group
az mysql server create \
    --name $SERVER_NAME \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --admin-user $ADMIN_USER \
    --admin-password $PASSWORD \
    --sku-name GP_Gen5_2

# Monitor usage metrics - CPU
az monitor metrics list \
    --resource "/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.DBforMySQL/servers/$SERVER_NAME" \
    --metric cpu_percent \
    --interval PT1M

# Monitor usage metrics - Storage
az monitor metrics list \
    --resource "/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.DBforMySQL/servers/$SERVER_NAME" \
    --metric storage_used \
    --interval PT1M

# Scale up the server by provisionining more vCores within the same tier
az mysql server update \
    --resource-group $RESOURCE_GROUP \
    --name $SERVER_NAME \
    --sku-name GP_Gen5_4

# Scale down the server by provisioning fewer vCores within the same tier
az mysql server update \
    --resource-group $RESOURCE_GROUP \
    --name $SERVER_NAME \
    --sku-name GP_Gen5_2

# Scale up the server to provision a storage size of 7GB
# Storage size cannot be reduced
az mysql server update \
    --resource-group $RESOURCE_GROUP \
    --name $SERVER_NAME \
    --storage-size 7168
```

## <a name="clean-up-deployment"></a>清理部署
运行脚本示例后，请使用以下命令删除资源组以及与其关联的所有资源。 

```cli
#!/bin/bash
RESOURCE_GROUP = "" # enter your resource group name
az group delete --name $RESOURCE_GROUP
```

## <a name="script-explanation"></a>脚本说明
此脚本使用下表中列出的命令：

| **命令** | **说明** |
|---|---|
| [az group create](/cli/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az mysql server create](/cli/mysql/server#az-mysql-server-create) | 创建用于托管数据库的 MySQL 服务器。 |
| [az mysql server update](/cli/mysql/server#az-mysql-server-update) | 更新 MySQL 服务器的属性。 |
| [az monitor metrics list](/cli/monitor/metrics#az-monitor-metrics-list) | 列出资源的指标值。 |
| [az group delete](/cli/group#az-group-delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure Database for MySQL 计算和存储](../concepts-pricing-tiers.md)
- 请尝试其他脚本：[用于 MySQL 的 Azure 数据库的 Azure CLI 示例](../sample-scripts-azure-cli.md)
- 详细了解 [Azure CLI](/cli/)