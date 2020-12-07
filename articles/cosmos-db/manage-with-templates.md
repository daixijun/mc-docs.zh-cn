---
title: 利用资源管理器模板创建和管理 Azure Cosmos DB
description: 使用 Azure 资源管理器模板创建和配置 Azure Cosmos DB for Core (SQL) API
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
origin.date: 10/14/2020
author: rockboyfor
ms.date: 12/07/2020
ms.testscope: yes|no
ms.testdate: 11/09/2020null
ms.author: v-yeche
ms.openlocfilehash: 7c7893d6f6a80d27850a7cb74da7d42faf7632b7
ms.sourcegitcommit: bbe4ee95604608448cf92dec46c5bfe4b4076961
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96598644"
---
<!--Verified successfully from rename article-->
<!--Verified successfully on 2020-06-01 preview-->
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-azure-resource-manager-templates"></a>利用 Azure 资源管理器模板管理 Azure Cosmos DB Core (SQL) API 资源
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

在本文中，你将了解如何使用 Azure 资源管理器模板来帮助部署和管理 Azure Cosmos DB 帐户、数据库和容器。

本文仅显示适用于 Core (SQL) API 帐户的 Azure 资源管理器模板示例。 你还可以查找 [Cassandra](templates-samples-cassandra.md)、[Gremlin](templates-samples-gremlin.md)、[MongoDB](templates-samples-mongodb.md) 和[表](templates-samples-table.md) API 的模板示例。

> [!IMPORTANT]
>
> * 帐户名称限制为 44 个字符，全部小写。
> * 若要更改吞吐量值，请用更新的 RU/s 重新部署模板。
> * 当你在 Azure Cosmos 帐户中添加或删除位置时，无法同时修改其他属性。 必须单独执行这些操作。
> * 无法重命名 Azure Cosmos DB 资源，因为这违反了 Azure 资源管理器与资源 URI 的工作方式。

若要创建以下任何 Azure Cosmos DB 资源，请将下列示例模板复制到新的 json 文件中。 在部署具有不同名称和值的同一资源的多个实例时，可以选择创建要使用的参数 json 文件。 可以通过多种方式部署 Azure 资源管理器模板，包括 [Azure 门户](../azure-resource-manager/templates/deploy-portal.md)、[Azure CLI](../azure-resource-manager/templates/deploy-cli.md)、[Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) 和 [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md)。

<a name="create-autoscale"></a>
<a name="create-resource"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>具有自动缩放吞吐量的 Azure Cosmos 帐户

此模板在两个区域创建一个 Azure Cosmos 帐户，其中包含用于一致性和故障转移的选项，以及为启用了大多数策略选项的自动缩放吞吐量配置的数据库和容器。 此模板还支持从 Azure 快速入门模板库进行一键部署。

[!INCLUDE [azure-raw-githubusercontent-azurechinacloud-environment-notice](../../includes/azure-raw-githubusercontent-azurechinacloud-environment-notice.md)]

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署到 Azure":::](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-autoscale%2Fazuredeploy.json)

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "defaultValue": "[concat('sql-', uniqueString(resourceGroup().id))]",
            "metadata": {
                "description": "Cosmos DB account name, max length 44 characters, lowercase"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the Cosmos DB account."
            }
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        },
        "defaultConsistencyLevel": {
            "type": "string",
            "defaultValue": "Session",
            "allowedValues": [ 
                "Eventual", 
                "ConsistentPrefix", 
                "Session", 
                "BoundedStaleness", 
                "Strong" 
            ],
            "metadata": {
                "description": "The default consistency level of the Cosmos DB account."
            }
        },
        "maxStalenessPrefix": {
            "type": "int",
            "minValue": 10,
            "defaultValue": 100000,
            "maxValue": 2147483647,
            "metadata": {
                "description": "Max stale requests. Required for BoundedStaleness. Valid ranges, Single Region: 10 to 1000000. Multi Region: 100000 to 1000000."
            }
        },
        "maxIntervalInSeconds": {
            "type": "int",
            "minValue": 5,
            "defaultValue": 300,
            "maxValue": 86400,
            "metadata": {
                "description": "Max lag time (minutes). Required for BoundedStaleness. Valid ranges, Single Region: 5 to 84600. Multi Region: 300 to 86400."
            }
        },
        "automaticFailover": {
            "type": "bool",
            "defaultValue": true,
            "allowedValues": [ 
                true, 
                false 
            ],
            "metadata": {
                "description": "Enable automatic failover for regions"
            }
        },
        "databaseName": {
            "type": "string",
            "metadata": {
                "description": "The name for the database"
            }
        },
        "containerName": {
            "type": "string",
            "metadata": {
                "description": "The name for the container"
            }
        },
        "autoscaleMaxThroughput": {
            "type": "int",
            "defaultValue": 4000,
            "minValue": 4000,
            "maxValue": 1000000,
            "metadata": {
                "description": "Maximum throughput for the container"
            }
        }
    },
    "variables": {
        "accountName": "[toLower(parameters('accountName'))]",
        "consistencyPolicy": {
            "Eventual": {
                "defaultConsistencyLevel": "Eventual"
            },
            "ConsistentPrefix": {
                "defaultConsistencyLevel": "ConsistentPrefix"
            },
            "Session": {
                "defaultConsistencyLevel": "Session"
            },
            "BoundedStaleness": {
                "defaultConsistencyLevel": "BoundedStaleness",
                "maxStalenessPrefix": "[parameters('maxStalenessPrefix')]",
                "maxIntervalInSeconds": "[parameters('maxIntervalInSeconds')]"
            },
            "Strong": {
                "defaultConsistencyLevel": "Strong"
            }
        },
        "locations": [
            {
                "locationName": "[parameters('primaryRegion')]",
                "failoverPriority": 0,
                "isZoneRedundant": false
            },
            {
                "locationName": "[parameters('secondaryRegion')]",
                "failoverPriority": 1,
                "isZoneRedundant": false
            }
        ]
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[variables('accountName')]",
            "apiVersion": "2020-04-01",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
                "locations": "[variables('locations')]",
                "databaseAccountOfferType": "Standard",
                "enableAutomaticFailover": "[parameters('automaticFailover')]"
            }
        },
        {
            "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases",
            "name": "[concat(variables('accountName'), '/', parameters('databaseName'))]",
            "apiVersion": "2020-04-01",
            "dependsOn": [ 
                "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]" 
            ],
            "properties":{
                "resource":{
                    "id": "[parameters('databaseName')]"
                }
            }
        },
        {
            "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers",
            "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('containerName'))]",
            "apiVersion": "2020-04-01",
            "dependsOn": [ 
                "[resourceId('Microsoft.DocumentDB/databaseAccounts/sqlDatabases', variables('accountName'), parameters('databaseName'))]" 
            ],
            "properties":
            {
                "resource":{
                    "id":  "[parameters('containerName')]",
                    "partitionKey": {
                        "paths": [
                            "/myPartitionKey"
                        ],
                        "kind": "Hash"
                    },
                    "indexingPolicy": {
                        "indexingMode": "consistent",
                        "includedPaths": [
                            {
                                "path": "/*"
                            }
                        ],
                        "excludedPaths": [
                            {
                                "path": "/myPathToNotIndex/*"
                            }
                        ],
                        "compositeIndexes":[  
                            [
                                {
                                    "path":"/name",
                                    "order":"ascending"
                                },
                                {
                                    "path":"/age",
                                    "order":"descending"
                                }
                            ]
                        ],
                        "spatialIndexes": [
                            {
                                "path": "/path/to/geojson/property/?",
                                "types": [
                                    "Point",
                                    "Polygon",
                                    "MultiPolygon",
                                    "LineString"
                                ]
                            }
                        ]
                    },
                    "defaultTtl": 86400,
                    "uniqueKeyPolicy": {
                        "uniqueKeys": [
                            {
                                "paths": [
                                    "/phoneNumber"
                                ]
                            }
                        ]
                    }
                },
                "options": {
                    "autoscaleSettings": { 
                        "maxThroughput": "[parameters('autoscaleMaxThroughput')]" 
                    }
                }
            }
        }
    ]
}
```

<a name="create-analytical-store"></a>

<a name="azure-cosmos-account-with-analytical-store"></a>
## <a name="azure-cosmos-account-with-analytical-store"></a>具有分析存储的 Azure Cosmos 帐户

此模板在一个区域中创建一个 Azure Cosmos 帐户，其中包含启用了分析 TTL 的容器和手动或自动缩放吞吐量选项。 此模板还支持从 Azure 快速入门模板库进行一键部署。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署到 Azure":::](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-analytical-store%2Fazuredeploy.json)

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "defaultValue": "[concat('cosmos', uniqueString(resourceGroup().id))]",
            "metadata": {
                "description": "Cosmos DB account name"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the Cosmos DB account."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "database1",
            "metadata": {
                "description": "The name for the database"
            }
        },
        "containerName": {
            "type": "string",
            "defaultValue": "container1",
            "metadata": {
                "description": "The name for the container"
            }
        },
        "partitionKeyPath": {
            "type": "string",
            "defaultValue": "/partitionKey",
            "metadata": {
                "description": "The partition key for the container"
            }
        },
        "throughputPolicy": {
            "type": "string",
            "defaultValue": "Autoscale",
            "allowedValues": [
                "Manual",
                "Autoscale"
            ],
            "metadata": {
                "description": "The throughput policy for the container"
            }
        },
        "manualProvisionedThroughput": {
            "type": "int",
            "defaultValue": 400,
            "minValue": 400,
            "maxValue": 1000000,
            "metadata": {
                "description": "Throughput value when using Manual Throughput Policy for the container"
            }
        },
        "autoscaleMaxThroughput": {
            "type": "int",
            "defaultValue": 4000,
            "minValue": 4000,
            "maxValue": 1000000,
            "metadata": {
                "description": "Maximum throughput when using Autoscale Throughput Policy for the container"
            }
        }
    },
    "variables": {
        "accountName": "[toLower(parameters('accountName'))]",
        "locations": [
            {
                "locationName": "[parameters('location')]",
                "failoverPriority": 0,
                "isZoneRedundant": false
            }
        ],
        "throughputPolicy": {
            "Manual": {
                "Throughput": "[parameters('manualProvisionedThroughput')]"
            },
            "Autoscale": {
                "autoscaleSettings": { "maxThroughput": "[parameters('autoscaleMaxThroughput')]" }
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[variables('accountName')]",
            "apiVersion": "2020-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "Session"
                },
                "databaseAccountOfferType": "Standard",
                "locations": "[variables('locations')]",
                "enableAnalyticalStorage": true
            }
        },
        {
            "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases",
            "name": "[concat(variables('accountName'), '/', parameters('databaseName'))]",
            "apiVersion": "2020-04-01",
            "dependsOn": [
                "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]"
            ],
            "properties": {
                "resource": {
                    "id": "[parameters('databaseName')]"
                }
            }
        },
        {
            "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers",
            "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('containerName'))]",
            "apiVersion": "2020-04-01",
            "dependsOn": [
                "[resourceId('Microsoft.DocumentDB/databaseAccounts/sqlDatabases', variables('accountName'), parameters('databaseName'))]"
            ],
            "properties": {
                "resource": {
                    "id": "[parameters('containerName')]",
                    "partitionKey": {
                        "paths": [
                            "[parameters('partitionKeyPath')]"
                        ],
                        "kind": "Hash"
                    },
                    "analyticalStorageTtl": -1
                },
                "options": "[variables('throughputPolicy')[parameters('throughputPolicy')]]"
            }
        }
    ]
}
```

<a name="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-provisioned-throughput"></a><a name="azure-cosmos-account-with-standard-provisioned-throughput"></a>具有标准预配吞吐量的 Azure Cosmos 帐户

此模板在两个区域创建一个 Azure Cosmos 帐户，其中包含用于一致性和故障转移的选项，以及为启用了大多数策略选项的标准吞吐量配置的数据库和容器。 此模板还支持从 Azure 快速入门模板库进行一键部署。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署到 Azure":::](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "accountName": {
      "type": "string",
      "defaultValue": "[concat('sql-', uniqueString(resourceGroup().id))]",
      "metadata": {
        "description": "Cosmos DB account name, max length 44 characters"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for the Cosmos DB account."
      }
    },
    "primaryRegion": {
      "type": "string",
      "metadata": {
        "description": "The primary replica region for the Cosmos DB account."
      }
    },
    "secondaryRegion": {
      "type": "string",
      "metadata": {
        "description": "The secondary replica region for the Cosmos DB account."
      }
    },
    "defaultConsistencyLevel": {
      "type": "string",
      "defaultValue": "Session",
      "allowedValues": [ "Eventual", "ConsistentPrefix", "Session", "BoundedStaleness", "Strong" ],
      "metadata": {
        "description": "The default consistency level of the Cosmos DB account."
      }
    },
    "maxStalenessPrefix": {
      "type": "int",
      "defaultValue": 100000,
      "minValue": 10,
      "maxValue": 2147483647,
      "metadata": {
        "description": "Max stale requests. Required for BoundedStaleness. Valid ranges, Single Region: 10 to 1000000. Multi Region: 100000 to 1000000."
      }
    },
    "maxIntervalInSeconds": {
      "type": "int",
      "defaultValue": 300,
      "minValue": 5,
      "maxValue": 86400,
      "metadata": {
        "description": "Max lag time (minutes). Required for BoundedStaleness. Valid ranges, Single Region: 5 to 84600. Multi Region: 300 to 86400."
      }
    },
    "automaticFailover": {
      "type": "bool",
      "defaultValue": true,
      "allowedValues": [ true, false ],
      "metadata": {
        "description": "Enable automatic failover for regions"
      }
    },
    "databaseName": {
      "type": "string",
      "defaultValue": "myDatabase",
      "metadata": {
        "description": "The name for the database"
      }
    },
    "containerName": {
      "type": "string",
      "defaultValue": "myContainer",
      "metadata": {
        "description": "The name for the container"
      }
    },
    "throughput": {
      "type": "int",
      "defaultValue": 400,
      "minValue": 400,
      "maxValue": 1000000,
      "metadata": {
        "description": "The throughput for the container"
      }
    }
  },
  "variables": {
    "accountName": "[toLower(parameters('accountName'))]",
    "consistencyPolicy": {
      "Eventual": {
        "defaultConsistencyLevel": "Eventual"
      },
      "ConsistentPrefix": {
        "defaultConsistencyLevel": "ConsistentPrefix"
      },
      "Session": {
        "defaultConsistencyLevel": "Session"
      },
      "BoundedStaleness": {
        "defaultConsistencyLevel": "BoundedStaleness",
        "maxStalenessPrefix": "[parameters('maxStalenessPrefix')]",
        "maxIntervalInSeconds": "[parameters('maxIntervalInSeconds')]"
      },
      "Strong": {
        "defaultConsistencyLevel": "Strong"
      }
    },
    "locations": [
      {
        "locationName": "[parameters('primaryRegion')]",
        "failoverPriority": 0,
        "isZoneRedundant": false
      },
      {
        "locationName": "[parameters('secondaryRegion')]",
        "failoverPriority": 1,
        "isZoneRedundant": false
      }
    ]
  },
  "resources": [
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2020-06-01-preview",
      "name": "[variables('accountName')]",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "enableAutomaticFailover": "[parameters('automaticFailover')]"
      }
    },
    {
      "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases",
      "apiVersion": "2020-06-01-preview",
      "name": "[concat(variables('accountName'), '/', parameters('databaseName'))]",
      "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]" ],
      "properties": {
        "resource": {
          "id": "[parameters('databaseName')]"
        }
      }
    },
    {
      "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers",
      "apiVersion": "2020-06-01-preview",
      "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('containerName'))]",
      "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/sqlDatabases', variables('accountName'), parameters('databaseName'))]" ],
      "properties": {
        "resource": {
          "id": "[parameters('containerName')]",
          "partitionKey": {
            "paths": [
              "/myPartitionKey"
            ],
            "kind": "Hash"
          },
          "indexingPolicy": {
            "indexingMode": "consistent",
            "includedPaths": [
              {
                "path": "/*"
              }
            ],
            "excludedPaths": [
              {
                "path": "/myPathToNotIndex/*"
              }
            ],
            "compositeIndexes": [
              [
                {
                  "path": "/name",
                  "order": "ascending"
                },
                {
                  "path": "/age",
                  "order": "descending"
                }
              ]
            ],
            "spatialIndexes": [
              {
                "path": "/path/to/geojson/property/?",
                "types": [
                  "Point",
                  "Polygon",
                  "MultiPolygon",
                  "LineString"
                ]
              }
            ]
          },
          "defaultTtl": 86400,
          "uniqueKeyPolicy": {
            "uniqueKeys": [
              {
                "paths": [
                  "/phoneNumber"
                ]
              }
            ]
          }
        },
        "options": { "throughput": "[parameters('throughput')]" }
      }
    }
  ]
}
```

<a name="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>具有服务器端功能的 Azure Cosmos DB 容器

此模板创建包含存储过程、触发器和用户定义函数的 Azure Cosmos 帐户、数据库和容器。 此模板还支持从 Azure 快速入门模板库进行一键部署。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署到 Azure":::](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-container-sprocs%2Fazuredeploy.json)

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "defaultValue": "[concat('cosmos-', uniqueString(resourceGroup().id))]",
            "metadata": {
                "description": "Cosmos DB account name"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the Cosmos DB account."
            }
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        },
        "defaultConsistencyLevel": {
            "type": "string",
            "defaultValue": "Session",
            "allowedValues": [ "Eventual", "ConsistentPrefix", "Session", "BoundedStaleness", "Strong" ],
            "metadata": {
                "description": "The default consistency level of the Cosmos DB account."
            }
        },
        "maxStalenessPrefix": {
            "type": "int",
            "minValue": 10,
            "defaultValue": 100000,
            "maxValue": 2147483647,
            "metadata": {
                "description": "Max stale requests. Required for BoundedStaleness. Valid ranges, Single Region: 10 to 1000000. Multi Region: 100000 to 1000000."
            }
        },
        "maxIntervalInSeconds": {
            "type": "int",
            "minValue": 5,
            "defaultValue": 300,
            "maxValue": 86400,
            "metadata": {
                "description": "Max lag time (seconds). Required for BoundedStaleness. Valid ranges, Single Region: 5 to 84600. Multi Region: 300 to 86400."
            }
        },    
        "automaticFailover": {
            "type": "bool",
            "defaultValue": true,
            "allowedValues": [ true, false ],
            "metadata": {
                "description": "Enable automatic failover for regions"
            }
        },
        "databaseName": {
            "type": "string",
            "metadata": {
                "description": "The name for the Core (SQL) database"
            }
        },
        "containerName": {
            "type": "string",
            "defaultValue": "container1",
            "metadata": {
                "description": "The name for the Core (SQL) API container"
            }
        },
        "throughput": {
            "type": "int",
            "defaultValue": 400,
            "minValue": 400,
            "maxValue": 1000000,
            "metadata": {
                "description": "The throughput for the container"
            }            
        }
    },
    "variables": {
        "accountName": "[toLower(parameters('accountName'))]",
        "consistencyPolicy": {
            "Eventual": {
                "defaultConsistencyLevel": "Eventual"
            },
            "ConsistentPrefix": {
                "defaultConsistencyLevel": "ConsistentPrefix"
            },
            "Session": {
                "defaultConsistencyLevel": "Session"
            },
            "BoundedStaleness": {
                "defaultConsistencyLevel": "BoundedStaleness",
                "maxStalenessPrefix": "[parameters('maxStalenessPrefix')]",
                "maxIntervalInSeconds": "[parameters('maxIntervalInSeconds')]"
            },
            "Strong": {
                "defaultConsistencyLevel": "Strong"
            }
        },
        "locations": 
        [ 
            {
                "locationName": "[parameters('primaryRegion')]",
                "failoverPriority": 0,
                "isZoneRedundant": false
            }, 
            {
                "locationName": "[parameters('secondaryRegion')]",
                "failoverPriority": 1,
                "isZoneRedundant": false
            }
        ]
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[variables('accountName')]",
            "apiVersion": "2020-03-01",
            "location": "[parameters('location')]",
            "kind": "GlobalDocumentDB",
            "properties": {
                "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
                "locations": "[variables('locations')]",
                "databaseAccountOfferType": "Standard",
                "enableAutomaticFailover": "[parameters('automaticFailover')]"
            }
        },
        {
            "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases",
            "name": "[concat(variables('accountName'), '/', parameters('databaseName'))]",
            "apiVersion": "2020-03-01",
            "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]" ],
            "properties":{
                "resource":{
                    "id": "[parameters('databaseName')]"
                }
            }
        },
        {
            "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers",
            "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('containerName'))]",
            "apiVersion": "2020-03-01",
            "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/sqlDatabases', variables('accountName'), parameters('databaseName'))]" ],
            "properties":
            {
                "resource":{
                    "id":  "[parameters('containerName')]",
                    "partitionKey": {
                        "paths": ["/myPartitionKey"],
                        "kind": "Hash"
                    },
                    "indexingPolicy": {
                        "indexingMode": "consistent",
                        "includedPaths": [{ "path": "/*" } ],
                        "excludedPaths": [{ "path": "/myPathToNotIndex/*" } ]
                    }
                },
                "options": { "throughput": "[parameters('throughput')]" }
            },
            "resources":
            [
                {
                    "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/storedProcedures",
                    "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('containerName'), '/myStoredProcedure')]",
                    "apiVersion": "2020-03-01",
                    "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers', variables('accountName'), parameters('databaseName'), parameters('containerName'))]" ],
                    "properties": {
                        "resource": {
                        "id": "myStoredProcedure",
                        "body": "function () { var context = getContext(); var response = context.getResponse(); response.setBody('Hello, World'); }"
                        }
                    }
                },
                {
                    "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/triggers",
                    "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('containerName'), '/myPreTrigger')]",
                    "apiVersion": "2020-03-01",
                    "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers', variables('accountName'), parameters('databaseName'), parameters('containerName'))]" ],
                    "properties": {
                        "resource": {
                        "id": "myPreTrigger",
                        "triggerType": "Pre",
                        "triggerOperation": "Create",
                        "body": "function validateToDoItemTimestamp(){var context=getContext();var request=context.getRequest();var itemToCreate=request.getBody();if(!('timestamp'in itemToCreate)){var ts=new Date();itemToCreate['timestamp']=ts.getTime();}request.setBody(itemToCreate);}"
                        }
                    }
                },
                {
                    "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/userDefinedFunctions",
                    "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('containerName'), '/myUserDefinedFunction')]",
                    "apiVersion": "2020-03-01",
                    "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers', variables('accountName'), parameters('databaseName'), parameters('containerName'))]" ],
                    "properties": {
                        "resource": {
                        "id": "myUserDefinedFunction",
                        "body": "function tax(income){if(income==undefined)throw'no input';if(income<1000)return income*0.1;else if(income<10000)return income*0.2;else return income*0.4;}"
                        }
                    }
                }
            ]
        }
    ]
}
```

<a name="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>免费层 Azure Cosmos DB 帐户

此模板创建一个免费层 Azure Cosmos 帐户和一个具有共享吞吐量的数据库，最多可以共享 25 个容器。 此模板还支持从 Azure 快速入门模板库进行一键部署。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署到 Azure":::](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-free%2Fazuredeploy.json)

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "defaultValue": "[concat('cosmos-', uniqueString(resourceGroup().id))]",
            "metadata": {
                "description": "Cosmos DB account name"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the Cosmos DB account."
            }
        },
        "databaseName": {
            "type": "string",
            "metadata": {
                "description": "The name for the Core (SQL) database"
            }
        }
    },
    "variables": {
        "accountName": "[toLower(parameters('accountName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "apiVersion": "2020-03-01",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "properties": {
                "enableFreeTier": true,
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "Session"
                }
            }
        },
        {
            "type": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases",
            "apiVersion": "2020-03-01",
            "name": "[concat(variables('accountName'), '/', parameters('databaseName'))]",
            "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]" ],
            "properties": {
                "resource": {
                    "id": "[parameters('databaseName')]"
                },
                "options": {"throughput": 400}
            }
        }
    ]
}
```

## <a name="next-steps"></a>后续步骤

下面是一些其他资源：

* [Azure 资源管理器文档](../azure-resource-manager/index.yml)
    
<!--Not Available on  - [Azure Cosmos DB resource provider schema](https://docs.microsoft.com/azure/templates/microsoft.documentdb/allversions)-->
    
* [Azure Cosmos DB 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [排查常见 Azure 资源管理器部署错误](../azure-resource-manager/templates/common-deployment-errors.md)

<!-- Update_Description: update meta properties, wording update, update link -->
