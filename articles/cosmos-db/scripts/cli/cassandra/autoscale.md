---
title: 为 Azure Cosmos DB 创建 Cassandra 密钥空间和表（具有自动缩放功能）
description: 为 Azure Cosmos DB 创建 Cassandra 密钥空间和表（具有自动缩放功能）
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
origin.date: 07/29/2020
author: rockboyfor
ms.date: 12/07/2020
ms.testscope: yes
ms.testdate: 08/10/2020
ms.author: v-yeche
ms.openlocfilehash: 5bfa9447cf6c2d2c337461650be59bb89c70afcf
ms.sourcegitcommit: bbe4ee95604608448cf92dec46c5bfe4b4076961
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96598625"
---
<!--Verify successfully-->
# <a name="create-an-azure-cosmos-cassandra-api-account-keyspace-and-table-with-autoscale-using-azure-cli"></a>使用 Azure CLI 创建 Azure Cosmos Cassandra API 帐户、密钥空间和表（具有自动缩放功能）
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.9.1 或更高版本。 

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

## <a name="sample-script"></a>示例脚本

```azurecli
#!/bin/bash
# Reference: az cosmosdb | https://docs.azure.cn/cli/cosmosdb
# --------------------------------------------------
#
# Create a Cassandra keyspace and table with autoscale

# Variables for Cassandra API resources
uniqueId=$RANDOM
resourceGroupName="Group-$uniqueId"
location='chinanorth2'
accountName="cosmos-$uniqueId" #needs to be lower case
keySpaceName='keyspace1'
tableName='table1'
maxThroughput=4000 #minimum = 4000

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a Cosmos account for Cassandra API
az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --capabilities EnableCassandra \
    --default-consistency-level Eventual \
    --locations regionName='China North 2' failoverPriority=0 isZoneRedundant=False 

# Create a Cassandra Keyspace
az cosmosdb cassandra keyspace create \
    -a $accountName \
    -g $resourceGroupName \
    -n $keySpaceName

# Define the schema for the table
schema=$(cat << EOF 
{
    "columns": [
        {"name": "columna","type": "uuid"},
        {"name": "columnb","type": "int"},
        {"name": "columnc","type": "text"}
    ],
    "partitionKeys": [
        {"name": "columna"}
    ],
    "clusterKeys": [
        { "name": "columnb", "orderBy": "asc" }
    ]
}
EOF
)
# Persist schema to json file
echo "$schema" > "schema-$uniqueId.json"

# Create the Cassandra table
az cosmosdb cassandra table create \
    -a $accountName \
    -g $resourceGroupName \
    -k $keySpaceName \
    -n $tableName \
    --max-throughput $maxThroughput \
    --schema @schema-$uniqueId.json

# Clean up temporary schema file
rm -f "schema-$uniqueId.json"

```

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```azurecli
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.azure.cn/cli/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az cosmosdb create](https://docs.azure.cn/cli/cosmosdb#az_cosmosdb_create) | 创建 Azure Cosmos DB 帐户。 |
| [az cosmosdb cassandra keyspace create](https://docs.azure.cn/cli/cosmosdb/cassandra/keyspace#az_cosmosdb_cassandra_keyspace_create) | 创建 Azure Cosmos Cassandra 密钥空间。 |
| [az cosmosdb cassandra table create](https://docs.azure.cn/cli/cosmosdb/cassandra/table#az_cosmosdb_cassandra_table_create) | 创建 Azure Cosmos Cassandra 表。 |
| [az group delete](https://docs.azure.cn/cli/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。 |


<!--CORRECT ON [az cosmosdb cassandra table create]-->

## <a name="next-steps"></a>后续步骤

有关 Azure Cosmos DB CLI 的详细信息，请参阅 [Azure Cosmos DB CLI 文档](https://docs.azure.cn/cli/cosmosdb)。

可以在 [Azure Cosmos DB CLI GitHub 存储库](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)中找到所有 Azure Cosmos DB CLI 脚本示例。

<!-- Update_Description: update meta properties, wording update, update link -->