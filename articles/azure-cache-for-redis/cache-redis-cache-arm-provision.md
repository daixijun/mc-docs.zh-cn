---
title: 使用 Azure 资源管理器模板部署 Azure Cache for Redis
description: 了解如何使用 Azure 资源管理器模板（ARM 模板）部署 Azure Cache for Redis 资源。 为常见方案提供模板。
author: yegu-ms
ms.author: v-junlch
ms.service: cache
ms.topic: conceptual
ms.custom: subject-armqs
ms.date: 11/03/2020
ms.openlocfilehash: 1ba973d08952cbf7d703bb07be64c6a346de6bf9
ms.sourcegitcommit: f436acd1e2a0108918a6d2ee9a1aac88827d6e37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96508966"
---
# <a name="quickstart-create-an-azure-cache-for-redis-using-an-arm-template"></a>快速入门：使用 ARM 模板创建 Azure Redis 缓存

了解如何创建 Azure 资源管理器模板（ARM 模板），以便部署 Azure Cache for Redis。 该缓存可以用于现有存储帐户以保存诊断数据。 还将了解如何定义要部署的资源以及如何定义执行部署时指定的参数。 可将此模板用于自己的部署，或自定义此模板以满足要求。 目前，对订阅的同一区域中的所有缓存共享诊断设置。 更新区域中的一个缓存会影响该区域中的所有其他缓存。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![“部署到 Azure”](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**：如果没有 Azure 订阅，可在开始前创建一个 [试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。
* **存储帐户**：若要创建帐户，请参阅 [创建 Azure 存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)。 存储帐户用于诊断数据。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-redis-cache/)。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "redisCacheName": {
      "type": "string",
      "metadata": {
        "description": "Specify the name of the Azure Redis Cache to create."
      }
    },
    "location": {
      "defaultValue": "[resourceGroup().location]",
      "type": "string",
      "metadata": {
        "description": "Location of all resources"
      }
    },
    "redisCacheSKU": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specify the pricing tier of the new Azure Redis Cache."
      }
    },
    "redisCacheFamily": {
      "type": "string",
      "defaultValue": "C",
      "allowedValues": [
        "C",
        "P"
      ],
      "metadata": {
        "description": "Specify the family for the sku. C = Basic/Standard, P = Premium."
      }
    },
    "redisCacheCapacity": {
      "type": "int",
      "defaultValue": 1,
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "metadata": {
        "description": "Specify the size of the new Azure Redis Cache instance. Valid values: for C (Basic/Standard) family (0, 1, 2, 3, 4, 5, 6), for P (Premium) family (1, 2, 3, 4)"
      }
    },
    "enableNonSslPort": {
      "type": "bool",
      "defaultValue": false,
      "metadata": {
        "description": "Specify a boolean value that indicates whether to allow access via non-SSL ports."
      }
    },
    "diagnosticsEnabled": {
      "type": "bool",
      "defaultValue": false,
      "metadata": {
        "description": "Specify a boolean value that indicates whether diagnostics should be saved to the specified storage account."
      }
    },
    "existingDiagnosticsStorageAccountId": {
      "type": "string",
      "metadata": {
        "description": "Specify an existing storage account for diagnostics."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Cache/Redis",
      "apiVersion": "2020-06-01",
      "name": "[parameters('redisCacheName')]",
      "location": "[parameters('location')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "minimumTlsVersion": "1.2",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
      "resources": [
        {
          "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
          "apiVersion": "2017-05-01-preview",
          "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/', parameters('redisCacheName'))]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Cache/Redis', parameters('redisCacheName'))]"
          ],
          "properties": {
            "storageAccountId": "[parameters('existingDiagnosticsStorageAccountId')]",
            "metrics": [
              {
                "timeGrain": "AllMetrics",
                "enabled": "[parameters('diagnosticsEnabled')]",
                "retentionPolicy": {
                  "days": 90,
                  "enabled": "[parameters('diagnosticsEnabled')]"
                }
              }
            ]
          }
        }
      ]
    }
  ]
}
```

该模板中定义了以下资源：

* [Microsoft.Cache/Redis](https://docs.microsoft.com/azure/templates/microsoft.cache/redis)
* [Microsoft.Insights/diagnosticsettings](https://docs.microsoft.com/azure/templates/microsoft.insights/diagnosticsettings)

适用于新[高级层](cache-overview.md#service-tiers)的资源管理器模板也已推出。

* [通过群集创建高级 Azure Redis 缓存](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
* [通过数据暂留创建高级 Azure Redis 缓存](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
* [Create Premium Redis Cache deployed into a Virtual Network](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)（创建部署到虚拟网络中的高级 Redis 缓存）

若要检查最新模板，请参阅 [Azure 快速入门模板](https://azure.microsoft.com/documentation/templates/)并搜索 Azure Cache for Redis。

## <a name="deploy-the-template"></a>部署模板

1. 选择下图登录到 Azure 并打开模板。

    [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)
1. 选择或输入以下值：

    * 订阅：选择用于创建数据共享和其他资源的 Azure 订阅。
    * 资源组：选择“新建”以创建新的资源组，或选择现有资源组。
    * **位置**：选择资源组的位置。 存储帐户和 Redis 缓存必须位于同一区域。 默认情况下，Redis 缓存使用的位置与资源组位置相同。 因此指定存储帐户所在的同一位置。
    * **Redis 缓存名称**：输入 Redis 缓存的名称。
    * **现有的诊断存储帐户**：输入存储帐户的资源 ID。 语法为 `/subscriptions/&lt;SUBSCRIPTION ID>/resourceGroups/&lt;RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/&lt;STORAGE ACCOUNT NAME>`。

    其余设置均采用默认值。
1. 选择“我同意上述条款和条件”，然后选择“购买” 。

## <a name="review-deployed-resources"></a>查看已部署的资源

1. 登录 [Azure 门户](https://portal.azure.cn)。
1. 打开创建的 Redis 缓存。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组，可以将其删除，这将删除资源组中的资源。

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建 Azure 资源管理器模板，以便部署 Azure Cache for Redis。 若要了解如何创建 Azure 资源管理器模板来部署包含 Azure Cache for Redis 的 Azure Web 应用，请参阅[使用模板创建 Web 应用和 Azure Cache for Redis](./cache-web-app-arm-with-redis-cache-provision.md)。

