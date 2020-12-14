---
title: Azure CLI 脚本 - 缩放和监视 Azure Database for PostgreSQL
description: Azure CLI 脚本示例 - 在查询指标后用于 PostgreSQL 服务器的 Azure 数据库缩放为不同的性能级别。
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc, devx-track-azurecli
ms.topic: sample
origin.date: 08/07/2019
ms.date: 12/14/2020
ms.openlocfilehash: 57177f9fa836cc293028a53a0582c0214b178a08
ms.sourcegitcommit: a8afac9982deafcf0652c63fe1615ba0ef1877be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96850732"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>使用 Azure CLI 监视和缩放单个 PostgreSQL 服务器
此示例 CLI 脚本在查询指标后为单个 Azure Database for PostgreSQL 服务器缩放计算和存储。 计算可以增加或减少。 存储只能增加。 

> [!IMPORTANT] 
> 存储只能增加，不能减少。

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

# Create a PostgreSQL server in the resource group
az postgres server create \
    --name $SERVER_NAME \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --admin-user $ADMIN_USER \
    --admin-password $PASSWORD \
    --sku-name GP_Gen5_2

# Monitor usage metrics - CPU
az monitor metrics list \
    --resource "/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.DBforPostgreSQL/servers/$SERVER_NAME" \
    --metric cpu_percent \
    --interval PT1M

# Monitor usage metrics - Storage
az monitor metrics list \
    --resource "/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.DBforPostgreSQL/servers/$SERVER_NAME" \
    --metric storage_used \
    --interval PT1M

# Scale up the server by provisionining more vCores within the same tier
az postgres server update \
    --resource-group $RESOURCE_GROUP \
    --name $SERVER_NAME \
    --sku-name GP_Gen5_4

# Scale down the server by provisioning fewer vCores within the same tier
az postgres server update \
    --resource-group $RESOURCE_GROUP \
    --name $SERVER_NAME \
    --sku-name GP_Gen5_2

# Scale up the server to provision a storage size of 7GB
# Storage size cannot be reduced
az postgres server update \
    --resource-group $RESOURCE_GROUP \
    --name $SERVER_NAME \
    --storage-size 7168
```

## <a name="clean-up-deployment"></a>清理部署
运行脚本示例后，请使用以下命令删除资源组以及与其关联的所有资源。 

```bash
#!/bin/bash
RESOURCE_GROUP = "" # enter your resource group name
az group delete --name $RESOURCE_GROUP
```

## <a name="script-explanation"></a>脚本说明
此脚本使用下表中列出的命令：

| **命令** | **说明** |
|---|---|
| [az group create](/cli/group) | 创建用于存储所有资源的资源组。 |
| [az postgres server create](/cli/postgres/server#az-postgres-server-create) | 创建托管数据库的 PostgreSQL 服务器。 |
| [az postgres server update](/cli/postgres/server#az-postgres-server-update) | 更新 PostgreSQL 服务器的属性。 |
| [az monitor metrics list](/cli/monitor/metrics) | 列出资源的指标值。 |
| [az group delete](/cli/group) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure Database for PostgreSQL 计算和存储](../concepts-pricing-tiers.md)
- 请尝试其他脚本：[用于 PostgreSQL 的 Azure 数据库的 Azure CLI 示例](../sample-scripts-azure-cli.md)
- 详细了解 [Azure CLI](/cli/)
