---
title: 快速入门 - 使用 Azure 资源管理器模板部署 NSG 流日志
description: 了解如何使用 Azure 资源管理器模板（ARM 模板）和 Azure PowerShell 以编程方式启用 NSG 流日志。
services: network-watcher
Customer intent: I need to enable the NSG flow logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
origin.date: 07/22/2020
author: rockboyfor
ms.date: 11/09/2020
ms.testscope: yes
ms.testdate: 10/19/2020
ms.author: v-yeche
ms.custom: subject-armqs
ms.openlocfilehash: 0618de4140f55d2e7f9194b392487bd78db8dbe2
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94327939"
---
<!--Verified successfully-->
# <a name="quickstart-configure-nsg-flow-logs-using-an-arm-template"></a>快速入门：使用 ARM 模板配置 NSG 流日志

本快速入门介绍如何使用[ Azure 资源管理器](../azure-resource-manager/management/overview.md)模板（ARM 模板）和 Azure PowerShell 启用 [NSG 流日志](network-watcher-nsg-flow-logging-overview.md)。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

首先，提供 NSG 流日志对象属性的概述，再提供一些示例模板。 然后，使用本地 PowerShell 实例部署模板。

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[!INCLUDE [azure-raw-githubusercontent-azurechinacloud-environment-notice](../../includes/azure-raw-githubusercontent-azurechinacloud-environment-notice.md)]

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署到 Azure":::](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-networkwatcher-flowlogs-create)。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Region where you resources are located"
            }
        },
        "NetworkWatcherName": {
            "type": "string",
            "defaultValue": "[concat('NetworkWatcher_', parameters('location'))]",
            "metadata": {
                "description": "Name of the Network Watcher attached to your subscription. Format: NetworkWatcher_<region_name>"
            }
        },
        "FlowLogName": {
            "type": "string",
            "defaultValue": "FlowLog1",
            "metadata": {
                "description": "Chosen name of your Flow log resource"
            }
        },
        "existingNSG": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the target NSG"
            }
        },
        "RetentionDays": {
            "type": "int",
            "defaultValue": 0,
            "minValue": 0,
            "maxValue": 365,
            "metadata": {
                "description": "Retention period in days. Default is zero which stands for permanent retention. Can be any Integer from 0 to 365"
            }
        },
        "FlowLogsversion": {
            "type": "string",
            "defaultValue": "2",
            "allowedValues": [
                "1",
                "2"
            ],
            "metadata": {
                "description": "FlowLogs Version. Correct values are 1 or 2 (default)"
            }
        },
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
                "Standard_LRS",
                "Standard_GRS"

            ],
            "metadata": {
                "description": "Storage Account type"
            }
        }
    },
    "variables": {
        "storageAccountEndPoint": "https://core.chinacloudapi.cn/",
        "storageAccountName": "[concat('flowlogs', uniquestring(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[variables('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {
            }
        },
        {
            "apiVersion": "2019-09-01",
            "name": "deployFlowLogs",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "NetworkWatcherRG",
            "dependsOn":[
                "[variables('storageAccountName')]"
            ],
            "properties": {
                "mode": "Incremental",
                "expressionEvaluationOptions": {
                    "scope": "inner"
                },
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "location": {
                            "type": "string"
                        },
                        "NetworkWatcherName": {
                            "type": "string"
                        },
                        "FlowLogName": {
                            "type": "string"
                        },
                        "existingNSG": {
                            "type": "string"
                        },
                        "RetentionDays": {
                            "type": "int"
                        },
                        "FlowLogsversion": {
                            "type": "string"
                        },
                        "storageAccountResourceId": {
                            "type": "string"
                        }
                    },
                    "resources": [
                        {
                            "name": "[concat(parameters('NetworkWatcherName'), '/', parameters('FlowLogName'))]",
                            "type": "Microsoft.Network/networkWatchers/FlowLogs",
                            "location": "[parameters('location')]",
                            "apiVersion": "2019-11-01",
                            "properties": {
                                "targetResourceId": "[parameters('existingNSG')]",
                                "storageId": "[parameters('storageAccountResourceId')]",
                                "enabled": true,
                                "retentionPolicy": {
                                    "days": "[parameters('RetentionDays')]",
                                    "enabled": true
                                },
                                "format": {
                                    "type": "JSON",
                                    "version": "[parameters('FlowLogsversion')]"
                                }
                            }
                        }
                    ]
                },
                "parameters": {
                        "location": {
                            "value": "[parameters('location')]"
                        },
                        "NetworkWatcherName": {
                            "value": "[parameters('NetworkWatcherName')]"
                        },
                        "FlowLogName": {
                            "value": "[parameters('FlowLogName')]"
                        },
                        "existingNSG": {
                            "value": "[parameters('existingNSG')]"
                        },
                        "RetentionDays": {
                            "value": "[parameters('RetentionDays')]"
                        },
                        "FlowLogsversion": {
                            "value": "[parameters('FlowLogsversion')]"
                        },
                        "storageAccountResourceId": {
                            "value": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                        }
                }
            }
        }
    ]
}
```

该模板中定义了多个资源：

- Microsoft.Storage/storageAccounts
- Microsoft.Resources/deployments

## <a name="nsg-flow-logs-object"></a>NSG 流日志对象

下面显示了包含所有参数的 NSG 流日志对象。

<!--Not Available on [Microsoft.Network networkWatchers/flowLogs](https://docs.microsoft.com/azure/templates/microsoft.network/networkwatchers/flowlogs)-->

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
        "enabled": "boolean",
        "workspaceResourceId": "string",
        "trafficAnalyticsInterval": "integer"
      },
      "retentionPolicy": {
        "days": "integer",
        "enabled": "boolean"
      },
      "format": {
        "type": "string",
        "version": "integer"
      }
    }
  }
}
```

若要创建 `Microsoft.Network/networkWatchers/flowLogs` 资源，请将以上 JSON 添加到模板的资源部分。

## <a name="creating-your-template"></a>创建模板

如果是首次使用 ARM 模板，可以通过以下链接详细了解这些模板。

- [使用 ARM 模板和 Azure PowerShell 部署资源](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
- 创建和部署你的第一个 ARM 模板[

本快速入门中使用的模板来自 [Azure 快速启动模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-networkwatcher-flowlogs-create)。

下面的完整模板示例是最简单的版本，只需传递最少的参数即可设置 NSG 流日志。 有关更多示例，请转到本[操作指南](network-watcher-nsg-flow-logging-azure-resource-manager.md)。

**示例**：以下模板在目标 NSG 上启用了 NSG 流日志，并将它们存储在给定的存储帐户中。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_chinaeastchinanorth/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "chinaeastchinanorth",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/<subscription Id>/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/<subscription Id>/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
        "enabled": true,
        "flowAnalyticsConfiguration": {},
        "retentionPolicy": {},
        "format": {}
      }
    }
  ]
}
```

> [!NOTE]
> - 资源名称采用 Parent Resource_Child resource 格式。 在这里，父资源为区域网络观察程序实例（格式：NetworkWatcher_RegionName。 示例：NetworkWatcher_chinaeastchinanorth)
> - `targetResourceId` 是目标 NSG 的资源 ID。
> - `storageId` 是目标存储帐户的资源 ID。

## <a name="deploy-the-template"></a>部署模板

本教程假定你已有一个资源组和一个可以启用流登录的 NSG。
可以在本地将上述任何示例模板保存为 `azuredeploy.json`。 更新属性值，使其指向订阅中的有效资源。

若要部署模板，请在 PowerShell 中运行以下命令。

```powershell
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> 上述命令会将资源部署到 NetworkWatcherRG 资源组，而不是包含 NSG 的资源组

## <a name="validate-the-deployment"></a>验证部署

可以通过多种方法来检查部署是否成功。 PowerShell 控制台应将 `ProvisioningState` 显示为 `Succeeded`。 此外，还可以访问 [NSG 流日志门户页面](https://portal.azure.cn/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)来确认所做的更改。 如果部署出现问题，请参阅[排查使用 Azure 资源管理器时的常见 Azure 部署错误](../azure-resource-manager/templates/common-deployment-errors.md)。

## <a name="clean-up-resources"></a>清理资源

Azure 可通过 `Complete` 部署模式删除资源。 若要删除流日志资源，请在 `Complete` 模式下指定部署，而不包含要删除的资源。 详细了解[完成部署模式](../azure-resource-manager/templates/deployment-modes.md#complete-mode)。

此外，可以根据以下步骤在 Azure 门户中禁用 NSG 流日志：

1. 登录到 Azure 门户
1. 在门户左上角选择“所有服务”。 在“筛选器”框中，键入“网络观察程序”。 搜索结果中出现“网络观察程序”后，将其选中。
1. 在“日志”下，选择“NSG 流日志” 。
1. 从 NSG 列表中，选择要为其禁用流日志的 NSG。
1. 在“流日志设置”下，将流日志状态设置为“关闭” 。
1. 向下滚动并选择“保存”。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你启用了 NSG 流日志。 现在，你必须了解如何使用以下工具直观显示 NSG 流数据：

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [打开源工具](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Azure 流量分析](traffic-analytics.md)

<!-- Update_Description: update meta properties, wording update, update link -->