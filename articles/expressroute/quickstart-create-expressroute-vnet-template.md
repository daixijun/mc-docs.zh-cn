---
title: 快速入门：使用 Azure 资源管理器模板（ARM 模板）创建 ExpressRoute 线路
description: 本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）创建 ExpressRoute 线路。
services: expressroute
author: duongau
mnager: kumud
ms.service: expressroute
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 10/12/2020
ms.author: duau
ms.openlocfilehash: dc02b3e518e35728d64f59a57242ea65724aaaf3
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329065"
---
# <a name="quickstart-create-an-expressroute-circuit-with-private-peering-using-an-arm-template"></a>快速入门：使用 ARM 模板创建具有专用对等互连的 ExpressRoute 线路

本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）来创建具有专用对等互连的 ExpressRoute 线路。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-expressroute-private-peering-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-expressroute-private-peering-vnet)。

在本快速入门中，你将创建一个以 Equinix 为服务提供商的 ExpressRoute 线路。 该线路将使用高级 SKU，其带宽为 50 Mbps，对等互连位置为华盛顿特区  。 将使用主要子网 192.168.10.16/30 和辅助子网 192.168.10.20/30 来启用专用对等互连 。 还将创建虚拟网络以及 HighPerformance ExpressRoute 网关。

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources deployed in the ARM template"
      }
    },
    "erpeeringLocation": {
      "type": "string",
      "defaultValue": "Shanghai",
      "metadata": {
        "description": "ExpressRoute peering location"
      }
    },
    "erCircuitName": {
      "type": "string",
      "defaultValue": "er-ckt01",
      "metadata": {
        "description": "Name of the ExpressRoute circuit"
      }
    },
    "serviceProviderName": {
      "type": "string",
      "defaultValue": "Shanghai Telecom Ethernet",
      "metadata": {
        "description": "Name of the ExpressRoute provider"
      }
    },
    "erSKU_Tier": {
      "type": "string",
      "defaultValue": "Premium",
      "allowedValues": [
        "Premium",
        "Standard"
      ],
      "metadata": {
        "description": "Tier ExpressRoute circuit"
      }
    },
    "erSKU_Family": {
      "type": "string",
      "defaultValue": "MeteredData",
      "allowedValues": [
        "MeteredData",
        "UnlimitedData"
      ],
      "metadata": {
        "description": "Billing model ExpressRoute circuit"
      }
    },
    "bandwidthInMbps": {
      "type": "int",
      "defaultValue": 50,
      "allowedValues": [
        50,
        100,
        200,
        500,
        1000,
        2000,
        5000,
        10000
      ],
      "metadata": {
        "description": "Bandwidth ExpressRoute circuit"
      }
    },
    "peerASN": {
      "type": "int",
      "defaultValue": 65001,
      "metadata": {
        "description": "autonomous system number used to create private peering between the customer edge router and MSEE routers"
      }
    },
    "primaryPeerAddressPrefix": {
      "type": "string",
      "defaultValue": "192.168.10.16/30",
      "metadata": {
        "description": "point-to-point network prefix of primary link between the customer edge router and MSEE router"
      }
    },
    "secondaryPeerAddressPrefix": {
      "type": "string",
      "defaultValue": "192.168.10.20/30",
      "metadata": {
        "description": "point-to-point network prefix of secondary link between the customer edge router and MSEE router"
      }
    },
    "vlanId": {
      "type": "int",
      "defaultValue": 100,
      "metadata": {
        "description": "VLAN Id used between the customer edge routers and MSEE routers. primary and secondary link have the same VLAN Id"
      }
    },
    "vnetName": {
      "type": "string",
      "defaultValue": "vnet1",
      "metadata": {
        "description": "name of the Virtual Network"
      }
    },
    "subnet1Name": {
      "type": "string",
      "defaultValue": "subnet1",
      "metadata": {
        "description": "name of the subnet"
      }
    },
    "vnetAddressSpace": {
      "type": "string",
      "defaultValue": "10.10.10.0/24",
      "metadata": {
        "description": "address space assigned to the Virtual Network"
      }
    },
    "subnet1Prefix": {
      "type": "string",
      "defaultValue": "10.10.10.0/25",
      "metadata": {
        "description": "network prefix assigned to the subnet"
      }
    },
    "gatewaySubnetPrefix": {
      "type": "string",
      "defaultValue": "10.10.10.224/27",
      "metadata": {
        "description": "network prefixes assigned to the gateway subnet. It has to be a network prefix with mask /27 or larger"
      }
    },
    "gatewayName": {
      "type": "string",
      "defaultValue": "er-gw",
      "metadata": {
        "description": "name of the ExpressRoute Gateway"
      }
    },
    "gatewaySku": {
      "type": "string",
      "defaultValue": "HighPerformance",
      "allowedValues": [
        "Standard",
        "HighPerformance",
        "UltraPerformance",
        "ErGw1AZ",
        "ErGw2AZ",
        "ErGw3AZ"
      ],
      "metadata": {
        "description": "ExpressRoute Gateway SKU"
      }
    }
  },
  "variables": {
    "location": "[parameters('location')]",
    "erlocation": "[parameters('location')]",
    "erCircuitName": "[parameters('erCircuitName')]",
    "serviceProviderName": "[parameters('serviceProviderName')]",
    "erpeeringLocation": "[parameters('erpeeringLocation')]",
    "erSKU_Tier": "[parameters('erSKU_Tier')]",
    "erSKU_Family": "[parameters('erSKU_Family')]",
    "erSKU_Name": "[concat(variables('erSKU_Tier'),'_',variables('erSKU_Family'))]",
    "bandwidthInMbps": "[parameters('bandwidthInMbps')]",
    "peerASN": "[parameters('peerASN')]",
    "primaryPeerAddressPrefix": "[parameters('primaryPeerAddressPrefix')]",
    "secondaryPeerAddressPrefix": "[parameters('secondaryPeerAddressPrefix')]",
    "vlanId": "[parameters('vlanId')]",
    "vnetName": "[parameters('vnetName')]",
    "subnet1Name": "[parameters('subnet1Name')]",
    "vnetAddressSpace": "[parameters('vnetAddressSpace')]",
    "subnet1Prefix": "[parameters('subnet1Prefix')]",
    "gatewaySubnetPrefix": "[parameters('gatewaySubnetPrefix')]",
    "gatewayName": "[parameters('gatewayName')]",
    "gatewayPublicIPName": "[concat(variables('gatewayName'),'-pubIP')]",
    "gatewaySku": "[parameters('gatewaySku')]",
    "nsg": "nsg"
  },
  "resources": [
    {
      "comments": "Create the ER circuit",
      "type": "Microsoft.Network/expressRouteCircuits",
      "apiVersion": "2020-06-01",
      "name": "[variables('erCircuitName')]",
      "location": "[variables('erlocation')]",
      "sku": {
        "name": "[variables('erSKU_Name')]",
        "tier": "[variables('erSKU_Tier')]",
        "family": "[variables('erSKU_Family')]"
      },
      "properties": {
        "serviceProviderProperties": {
          "serviceProviderName": "[variables('serviceProviderName')]",
          "peeringLocation": "[variables('erpeeringLocation')]",
          "bandwidthInMbps": "[variables('bandwidthInMbps')]"
        },
        "allowClassicOperations": false
      }
    },
    {
      "comments": "Create ER private peering",
      "type": "Microsoft.Network/expressRouteCircuits/peerings",
      "apiVersion": "2020-06-01",
      "name": "[concat(variables('erCircuitName'),'/','AzurePrivatePeering')]",
      "location": "[variables('erlocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/expressRouteCircuits', variables('erCircuitName'))]"
      ],
      "properties": {
        "peeringType": "AzurePrivatePeering",
        "peerASN": "[variables('peerASN')]",
        "primaryPeerAddressPrefix": "[variables('primaryPeerAddressPrefix')]",
        "secondaryPeerAddressPrefix": "[variables('secondaryPeerAddressPrefix')]",
        "vlanId": "[variables('vlanId')]"
      }
    },
    {
      "comments": "NSG applied to the subnets in VNet1",
      "type": "Microsoft.Network/networkSecurityGroups",
      "apiVersion": "2020-06-01",
      "name": "[variables('nsg')]",
      "location": "[variables('location')]",
      "properties": {
        "securityRules": [
          {
            "name": "SSH-rule",
            "properties": {
              "description": "allow SSH",
              "protocol": "Tcp",
              "sourcePortRange": "*",
              "destinationPortRange": "22",
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "VirtualNetwork",
              "access": "Allow",
              "priority": 500,
              "direction": "Inbound"
            }
          },
          {
            "name": "RDP-rule",
            "properties": {
              "description": "allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "VirtualNetwork",
              "access": "Allow",
              "priority": 600,
              "direction": "Inbound"
            }
          }
        ]
      }
    },
    {
      "comments": "it builds a Virtual Network with GatewaySubnet",
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2020-06-01",
      "name": "[variables('vnetName')]",
      "location": "[variables('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/networkSecurityGroups', variables('nsg'))]"
      ],
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetAddressSpace')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnet1Name')]",
            "properties": {
              "addressPrefix": "[variables('subnet1Prefix')]",
              "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('nsg'))]"
              }
            }
          },
          {
            "name": "GatewaySubnet",
            "properties": {
              "addressPrefix": "[variables('gatewaySubnetPrefix')]"
            }
          }
        ]
      }
    },
    {
      "comments": "public IP of the ExpressRoute Gateway. Only public IP Dynamic allocation is supported",
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2020-06-01",
      "name": "[variables('gatewayPublicIPName')]",
      "location": "[variables('location')]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic"
      }
    },
    {
      "comments": "ExpressRoute Gateway in the GatewaySubnet",
      "type": "Microsoft.Network/virtualNetworkGateways",
      "apiVersion": "2020-06-01",
      "name": "[variables('gatewayName')]",
      "location": "[variables('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/publicIPAddresses', variables('gatewayPublicIPName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks', variables('vnetName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "subnet": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('vnetName'), 'GatewaySubnet')]"
              },
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName'))]"
              }
            },
            "name": "gwIPconf"
          }
        ],
        "gatewayType": "ExpressRoute",
        "sku": {
          "name": "[variables('gatewaySku')]",
          "tier": "[variables('gatewaySku')]"
        },
        "vpnType": "RouteBased"
      }
    }
  ],
  "outputs": {
    "erCircuitName": {
      "type": "string",
      "value": "[variables('erCircuitName')]"
    },
    "gatewayName": {
      "type": "string",
      "value": "[variables('gatewayName')]"
    },
    "gatewaySku": {
      "type": "string",
      "value": "[variables('gatewaySku')]"
    }
  }
}
```

该模板中已定义了多个 Azure 资源：

* [**Microsoft.Network/expressRouteCircuits**](https://docs.microsoft.com/azure/templates/microsoft.network/expressRouteCircuits)
* [**Microsoft.Network/expressRouteCircuits/peerings**](https://docs.microsoft.com/azure/templates/microsoft.network/expressRouteCircuits/peerings)（用于线路上已启用的专用对等互连）
* [**Microsoft.Network/networkSecurityGroups**](https://docs.microsoft.com/azure/templates/microsoft.network/networkSecurityGroups)（网络安全组应用于虚拟网络中的子网）
* [**Microsoft.Network/virtualNetworks**](https://docs.microsoft.com/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft.Network/publicIPAddresses**](https://docs.microsoft.com/azure/templates/microsoft.network/publicIPAddresses)（公共 IP 由 ExpressRoute 网关使用）
* [**Microsoft.Network/virtualNetworkGateways**](https://docs.microsoft.com/azure/templates/microsoft.network/virtualNetworkGateways)（ExpressRoute 网关用于将 VNet 链接到线路）

若要查找与 ExpressRoute 相关的更多模板，请参阅 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)。

## <a name="deploy-the-template"></a>部署模板

1. 按照说明登录到 Azure。

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-private-peering-vnet/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    等到控制台中显示提示。

1. 从上一个代码块中选择“复制”，以复制 PowerShell 脚本。

1. 右键单击 shell 控制台窗格，然后选择“粘贴”。

1. 输入相应的值。

    资源组名称是追加了 **rg** 的项目名称。

    部署模板大约需要 20 分钟。 完成后，输出类似于：

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-powershell-output.png" alt-text="ExpressRoute 资源管理器模板 PowerShell 部署输出":::

使用 Azure PowerShell 部署模板。 除了 Azure PowerShell，还可以使用 Azure 门户、Azure CLI 和 REST API。 若要了解其他部署方法，请参阅[部署模板](../azure-resource-manager/templates/deploy-portal.md)。

## <a name="validate-the-deployment"></a>验证部署

1. 登录 [Azure 门户](https://portal.azure.cn)。

1. 从左侧窗格中选择“资源组”。

1. 选择你在上一部分中创建的资源组。 默认资源组名称是追加了 **rg** 的项目名称。

1. 资源组应包含以下资源：

     :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-resource-group.png" alt-text="ExpressRoute 部署资源组":::

1. 选择 ExpressRoute 线路 er-ck01 来验证线路状态是否为“已启用”，提供程序状态是否为“未预配”，专用对等互连的状态是否为“已预配”   。

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-circuit.png" alt-text="ExpressRoute 部署线路":::

> [!NOTE]
> 你将需要调用提供程序来完成预配过程，然后才能将虚拟网络链接到线路。

## <a name="clean-up-resources"></a>清理资源

如果不再需要通过 ExpressRoute 线路创建的资源，请删除该资源组。 这会删除 ExpressRoute 线路和所有相关资源。

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```azurepowershell
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们创建了：

* ExpressRoute 线路
* 虚拟网络
* VPN 网关
* 公共 IP
* 网络安全组

若要了解如何将虚拟网络链接到线路，请继续学习 ExpressRoute 教程。

> [!div class="nextstepaction"]
> [ExpressRoute 教程](expressroute-howto-linkvnet-portal-resource-manager.md)
