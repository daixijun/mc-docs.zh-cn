---
title: Azure 资源管理器：创建 Azure SQL 托管实例
description: 了解如何使用 Azure 资源管理器模板创建 Azure SQL 托管实例。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: subject-armqs
ms.devlang: ''
ms.topic: quickstart
author: WenJason
ms.author: v-jay
ms.reviewer: carlrab
origin.date: 06/22/2020
ms.date: 07/13/2020
ms.openlocfilehash: 6934e866b27fbdf920150cfb1aa3c7ae8640be58
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96432541"
---
# <a name="quickstart-create-an-azure-sql-managed-instance-using-an-arm-template"></a>快速入门：使用 ARM 模板创建 Azure SQL 托管实例

本快速入门重点介绍部署 Azure 资源管理器模板（ARM 模板）以创建 Azure SQL 托管实例和 vNet 的过程。 [Azure SQL 托管实例](sql-managed-instance-paas-overview.md)是一个智能、完全托管且可缩放的云数据库，与 SQL Server 数据库引擎之间的功能奇偶一致性达 100%。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![“部署到 Azure”](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sqlmi-new-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请[创建一个试用版订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "managedInstanceName": {
            "type": "string",
            "metadata": {
                "description": "Enter managed instance name."
            }
        },
        "administratorLogin": {
            "type": "string",
            "metadata": {
                "description": "Enter user name."
            }
        },
        "administratorLoginPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Enter password."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Enter location. If you leave this field blank resource group location would be used."
            }
        },
        "virtualNetworkName": {
            "type": "string",
            "defaultValue": "SQLMI-VNET",
            "metadata": {
                "description": "Enter virtual network name. If you leave this field blank name will be created by the template."
            }
        },
        "addressPrefix": {
            "type": "string",
            "defaultValue": "10.0.0.0/16",
            "metadata": {
                "description": "Enter virtual network address prefix."
            }
        },
        "subnetName": {
            "type": "string",
            "defaultValue": "ManagedInstance",
            "metadata": {
                "description": "Enter subnet name."
            }
        },
        "subnetPrefix": {
            "type": "string",
            "defaultValue": "10.0.0.0/24",
            "metadata": {
                "description": "Enter subnet address prefix."
            }
        },
        "skuName": {
            "type": "string",
            "allowedValues":[
                "GP_Gen5",
                "BC_Gen5"
            ],
            "defaultValue": "GP_Gen5",
            "metadata": {
                "description": "Enter sku name."
            }
        },
        "vCores": {
            "type": "int",
            "defaultValue": 16,
            "allowedValues":[
                8,
                16,
                24,
                32,
                40,
                64,
                80
            ],              
            "metadata": {
                "description": "Enter number of vCores."
            }
        },
        "storageSizeInGB": {
            "type": "int",         
            "defaultValue": 256,
            "minValue": 32,
            "maxValue": 8192,
            "metadata": {
                "description": "Enter storage size."
            }
        },
        "licenseType": {
            "type": "string",
            "defaultValue": "LicenseIncluded",
            "allowedValues":[
                "BasePrice",
                "LicenseIncluded"
            ],             
            "metadata": {
                "description": "Enter license type."
            }
        }
    },
    "variables": {
        "networkSecurityGroupName": "[concat('SQLMI-', parameters('managedInstanceName'), '-NSG')]",
        "routeTableName": "[concat('SQLMI-', parameters('managedInstanceName'), '-Route-Table')]"
    },
    "resources": [
        {
            "apiVersion": "2019-06-01",
            "type": "Microsoft.Network/networkSecurityGroups",
            "name": "[variables('networkSecurityGroupName')]",
            "location": "[parameters('location')]",
            "properties": {
              "securityRules": [
                  {
                    "name": "allow_tds_inbound",
                    "properties": {
                      "description": "Allow access to data",
                      "protocol": "Tcp",
                      "sourcePortRange": "*",
                      "destinationPortRange": "1433",
                      "sourceAddressPrefix": "VirtualNetwork",
                      "destinationAddressPrefix": "*",
                      "access": "Allow",
                      "priority": 1000,
                      "direction": "Inbound"
                    }
                  },
                  {
                    "name": "allow_redirect_inbound",
                    "properties": {
                        "description": "Allow inbound redirect traffic to Managed Instance inside the virtual network",
                        "protocol": "Tcp",
                        "sourcePortRange": "*",
                        "destinationPortRange": "11000-11999",
                        "sourceAddressPrefix": "VirtualNetwork",
                        "destinationAddressPrefix": "*",
                        "access": "Allow",
                        "priority": 1100,
                        "direction": "Inbound"
                    }
                  },                  
                  {
                    "name": "deny_all_inbound",
                    "properties": {
                      "description": "Deny all other inbound traffic",
                      "protocol": "*",
                      "sourcePortRange": "*",
                      "destinationPortRange": "*",
                      "sourceAddressPrefix": "*",
                      "destinationAddressPrefix": "*",
                      "access": "Deny",
                      "priority": 4096,
                      "direction": "Inbound"
                    }
                  },
                  {
                    "name": "deny_all_outbound",
                    "properties": {
                      "description": "Deny all other outbound traffic",
                      "protocol": "*",
                      "sourcePortRange": "*",
                      "destinationPortRange": "*",
                      "sourceAddressPrefix": "*",
                      "destinationAddressPrefix": "*",
                      "access": "Deny",
                      "priority": 4096,
                      "direction": "Outbound"
                    }
                  }
              ]
            }
        },
        {
            "type": "Microsoft.Network/routeTables",
            "name": "[variables('routeTableName')]",
            "apiVersion": "2019-06-01",
            "location": "[parameters('location')]",
            "properties": {
                "disableBgpRoutePropagation": false
            }
        },
        {
            "name": "[parameters('virtualNetworkName')]",
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2019-06-01",
            "dependsOn":[
                "[variables('routeTableName')]",
                "[variables('networkSecurityGroupName')]"
            ],
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[parameters('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetPrefix')]",
                            "routeTable": {
                                "id": "[resourceId('Microsoft.Network/routeTables', variables('routeTableName'))]"
                            },
                            "networkSecurityGroup": {
                                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
                            },
                            "delegations": [
                                {
                                    "name": "miDelegation",
                                    "properties": {
                                        "serviceName": "Microsoft.Sql/managedInstances"
                                    }
                                }
                            ]                               
                        }
                    }
                ]
            }
        },      
        {
            "type": "Microsoft.Sql/managedInstances",
            "apiVersion": "2019-06-01-preview",
            "dependsOn":[
                "[parameters('virtualNetworkName')]"
            ],
            "identity": {
                "type": "SystemAssigned"
            },
            "location": "[parameters('location')]",
            "name": "[parameters('managedInstanceName')]",
            "sku": {
                "name": "[parameters('skuName')]"
            },
            "properties": {
                "administratorLogin": "[parameters('administratorLogin')]",
                "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
                "subnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
                "storageSizeInGB": "[parameters('storageSizeInGB')]",
                "vCores": "[parameters('vCores')]",
                "licenseType": "[parameters('licenseType')]"
            }
        }
    ]
}
```

该模板中定义了以下资源：

- [**Microsoft.Network/networkSecurityGroups**](https://docs.microsoft.com/azure/templates/microsoft.Network/networkSecurityGroups)
- [**Microsoft.Network/routeTables**](https://docs.microsoft.com/azure/templates/microsoft.Network/routeTables)
- [**Microsoft.Network/virtualNetworks**](https://docs.microsoft.com/azure/templates/microsoft.Network/virtualNetworks)
- [**Microsoft.Sql/managedinstances**](https://docs.microsoft.com/azure/templates/microsoft.sql/managedinstances)

可以在 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular)中找到更多模板示例。

## <a name="deploy-the-template"></a>部署模板

> [!IMPORTANT]
> 部署托管实例是一项运行时间很长的操作。 通常情况下，子网中第一个实例的部署时间比子网中已经有托管实例时实例的部署时间要长得多。 若要了解平均预配时间，请参阅 [SQL 托管实例管理操作](sql-managed-instance-paas-overview.md#management-operations)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. chinaeast2)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. chinaeast2):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---

## <a name="review-deployed-resources"></a>查看已部署的资源

访问 [Azure 门户](https://portal.azure.cn/#blade/HubsExtension/BrowseResourceGroups)，并验证所选资源组中是否有托管实例。 由于创建托管实例可能需要一些时间，所以可能需要在资源组的“概述”页面中查看“部署”链接 。

- 有关介绍如何从 Azure 虚拟机连接到 SQL 托管实例的快速入门，请参阅[配置 Azure 虚拟机连接](connect-vm-instance-configure.md)。
- 有关介绍如何使用点到站点连接从本地客户端计算机连接到 SQL 托管实例的快速入门，请参阅[配置点到站点连接](point-to-site-p2s-configure.md)。

## <a name="clean-up-resources"></a>清理资源

若要执行[后续步骤](#next-steps)，请保留托管实例，但请在完成任何其他教程之后删除托管实例及相关资源。 删除托管实例之后，请参阅[在删除托管实例后删除子网](virtual-cluster-delete.md)。


若要删除资源组，请执行以下操作：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将 Azure VM 配置为连接到 Azure SQL 托管实例](connect-vm-instance-configure.md)
