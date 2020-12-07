---
title: 快速入门：使用资源管理器模板定向 Web 流量
titleSuffix: Azure Application Gateway
description: 本快速入门介绍如何使用资源管理器模板创建 Azure 应用程序网关，以便将 Web 流量定向到后端池中的虚拟机。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 09/01/2020
ms.author: v-junlch
ms.custom: mvc
ms.openlocfilehash: bac8b588a6fadc38ef56005e5e7c27ae5187ed91
ms.sourcegitcommit: f436acd1e2a0108918a6d2ee9a1aac88827d6e37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509080"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---arm-template"></a>快速入门：使用 Azure 应用程序网关定向 Web 流量 - ARM 模板

在本快速入门中，使用 Azure 资源管理器模板（ARM 模板）创建 Azure 应用程序网关。 然后，我们对应用程序网关进行测试，确保其正常运行。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

还可以使用 [Azure 门户](quick-create-portal.md)、[Azure PowerShell](quick-create-powershell.md) 或 [Azure CLI](quick-create-cli.md) 完成本快速入门。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [创建帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="review-the-template"></a>查看模板

为简单起见，此模板创建了一项简单设置，其中包含一个公共的前端 IP、一个在应用程序网关上托管单个站点的基本侦听器、一项基本的请求路由规则，以及两个位于后端池的虚拟机。

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/ag-docs-qs/)

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
               "description": "Admin username for the backend servers"
             }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
               "description": "Password for the admin account on the backend servers"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "vmSize": {
           "type": "string",
           "defaultValue": "Standard_B2ms",
           "metadata": {
              "description": "Size of the virtual machine."
            }
        }
    },
    "variables": {
        "virtualMachines_myVM_name": "myVM",
        "virtualNetworks_myVNet_name": "myVNet",
        "net_interface": "net-int",
        "ipconfig_name": "ipconfig",
        "publicIPAddress": "public_ip",
        "nsg_name": "vm-nsg",
        "applicationGateways_myAppGateway_name": "myAppGateway",
        "vnet_prefix": "10.0.0.0/16",
        "ag_subnet_prefix": "10.0.0.0/24",
        "backend_subnet_prefix": "10.0.1.0/24"
    },
    "resources": [
        {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-11-01",
            "name": "[concat(variables('nsg_name'), copyIndex(1))]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "RDP",
                        "properties": {
                            "protocol": "TCP",
                            "sourcePortRange": "*",
                            "destinationPortRange": "3389",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 300,
                            "direction": "Inbound"
                        }
                    }
                ]
            },
            "copy": {
              "name": "nsg-loop",
              "count": 2
            }            
        },
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-11-01",
            "name": "[concat(variables('publicIPAddress'), copyIndex())]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard"
            },
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Static",
                "idleTimeoutInMinutes": 4
            },
            "copy": {
              "name": "publicip-loop",
              "count": 3
            }            
        },
{
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2019-11-01",
            "name": "[variables('virtualNetworks_myVNet_name')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('vnet_prefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "myAGSubnet",
                        "properties": {
                            "addressPrefix": "[variables('ag_subnet_prefix')]",
                            "privateEndpointNetworkPolicies": "Enabled",
                            "privateLinkServiceNetworkPolicies": "Enabled"
                        }
                    },
                    {
                        "name": "myBackendSubnet",
                        "properties": {
                            "addressPrefix": "[variables('backend_subnet_prefix')]",
                            "privateEndpointNetworkPolicies": "Enabled",
                            "privateLinkServiceNetworkPolicies": "Enabled"
                        }
                    }
                ],
                "enableDdosProtection": false,
                "enableVmProtection": false
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2019-07-01",
            "name": "[concat(variables('virtualMachines_myVM_name'), copyIndex(1))]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('net_interface'), copyIndex(1)))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "2016-Datacenter",
                        "version": "latest"
                    },
                    "osDisk": {
                        "osType": "Windows",
                        "createOption": "FromImage",
                        "caching": "ReadWrite",
                        "managedDisk": {
                            "storageAccountType": "StandardSSD_LRS"
                        },
                        "diskSizeGB": 127
                    }
                },
                "osProfile": {
                    "computerName": "[concat(variables('virtualMachines_myVM_name'), copyIndex(1))]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]",
                    "windowsConfiguration": {
                        "provisionVMAgent": true,
                        "enableAutomaticUpdates": true
                    },
                    "allowExtensionOperations": true
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('net_interface'), copyIndex(1)))]"
                        }
                    ]
                }
            },
            "copy": {
              "name": "vm-loop",
              "count": 2
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "apiVersion": "2019-07-01",
            "name": "[concat(variables('virtualMachines_myVM_name'), copyIndex(1),'/IIS')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Compute/virtualMachines', concat(variables('virtualMachines_myVM_name'), copyIndex(1)))]"
            ],
            "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "typeHandlerVersion": "1.4",
                "settings": {
                    "commandToExecute": "powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"
                }
            },
            "copy": {
              "name": "ext-loop",
              "count": 2
            }
        },
        {
            "type": "Microsoft.Network/applicationGateways",
            "apiVersion": "2019-11-01",
            "name": "[variables('applicationGateways_myAppGateway_name')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworks_myVNet_name'))]",
                "[resourceId('Microsoft.Network/publicIPAddresses', concat(variables('publicIPAddress'), '0'))]"
            ],
            "properties": {
                "sku": {
                    "name": "Standard_v2",
                    "tier": "Standard_v2"
                },
                "gatewayIPConfigurations": [
                    {
                        "name": "appGatewayIpConfig",
                        "properties": {
                            "subnet": {
                                "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworks_myVNet_name'), 'myAGSubnet')]"
                            }
                        }
                    }
                ],
                "frontendIPConfigurations": [
                    {
                        "name": "appGwPublicFrontendIp",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses', concat(variables('publicIPAddress'), '0'))]"
                            }
                        }
                    }
                ],
                "frontendPorts": [
                    {
                        "name": "port_80",
                        "properties": {
                            "port": 80
                        }
                    }
                ],
                "backendAddressPools": [
                    {
                        "name": "myBackendPool",
                        "properties": {
                        }
                    }
                ],
                "backendHttpSettingsCollection": [
                    {
                        "name": "myHTTPSetting",
                        "properties": {
                            "port": 80,
                            "protocol": "Http",
                            "cookieBasedAffinity": "Disabled",
                            "pickHostNameFromBackendAddress": false,
                            "requestTimeout": 20
                        }
                    }
                ],
                "httpListeners": [
                    {
                        "name": "myListener",
                        "properties": {
                            "frontendIPConfiguration": {
                                "id": "[resourceId('Microsoft.Network/applicationGateways/frontendIPConfigurations', variables('applicationGateways_myAppGateway_name'), 'appGwPublicFrontendIp')]"
                            },
                            "frontendPort": {
                                "id": "[resourceId('Microsoft.Network/applicationGateways/frontendPorts', variables('applicationGateways_myAppGateway_name'), 'port_80')]"
                            },
                            "protocol": "Http",
                            "requireServerNameIndication": false
                        }
                    }
                ],
                "requestRoutingRules": [
                    {
                        "name": "myRoutingRule",
                        "properties": {
                            "ruleType": "Basic",
                            "httpListener": {
                                "id": "[resourceId('Microsoft.Network/applicationGateways/httpListeners', variables('applicationGateways_myAppGateway_name'), 'myListener')]"
                            },
                            "backendAddressPool": {
                                "id": "[resourceId('Microsoft.Network/applicationGateways/backendAddressPools', variables('applicationGateways_myAppGateway_name'), 'myBackendPool')]"
                            },
                            "backendHttpSettings": {
                                "id": "[resourceId('Microsoft.Network/applicationGateways/backendHttpSettingsCollection', variables('applicationGateways_myAppGateway_name'), 'myHTTPSetting')]"
                            }
                        }
                    }
                ],
                "enableHttp2": false,
                "autoscaleConfiguration": {
                    "minCapacity": 0,
                    "maxCapacity": 10
                }
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2019-11-01",
            "name": "[concat(variables('net_interface'), copyIndex(1))]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses', concat(variables('publicIPAddress'), copyIndex(1)))]",
                "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworks_myVNet_name'))]",
                "[resourceId('Microsoft.Network/applicationGateways', variables('applicationGateways_myAppGateway_name'))]",
                "[resourceId('Microsoft.Network/networkSecurityGroups', concat(variables('nsg_name'), copyIndex(1)))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "[concat(variables('ipconfig_name'), copyIndex(1))]",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses', concat(variables('publicIPAddress'), copyIndex(1)))]"
                            },
                            "subnet": {
                                "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworks_myVNet_name'), 'myBackendSubnet')]"
                            },
                            "primary": true,
                            "privateIPAddressVersion": "IPv4",
                            "applicationGatewayBackendAddressPools": [
                                {
                                    "id": "[resourceId('Microsoft.Network/applicationGateways/backendAddressPools', variables('applicationGateways_myAppGateway_name'), 'myBackendPool')]"
                                }
                            ]
                        }
                    }
                ],
                "enableAcceleratedNetworking": false,
                "enableIPForwarding": false,
                "networkSecurityGroup": {
                    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat(variables('nsg_name'), copyIndex(1)))]"
                }
            },
            "copy": {
              "name": "int-loop",
              "count": 2
            }
        }
    ]
}
```

模板中定义了多个 Azure 资源：

- [**Microsoft.Network/applicationgateways**](https://docs.microsoft.com/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/publicIPAddresses**](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)：一个用于应用程序网关，两个用于虚拟机。
- [**Microsoft.Network/networkSecurityGroups**](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)：两个虚拟机
- [**Microsoft.Network/networkInterfaces**](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)：两个用于虚拟机
- [**Microsoft.Compute/virtualMachine/extensions**](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions)：用于配置 IIS 和网页

## <a name="deploy-the-template"></a>部署模板

将 ARM 模板部署到 Azure：

1. 选择“部署到 Azure”，登录到 Azure 并打开模板。 该模板在运行 IIS 的后端池中创建应用程序网关、网络基础结构和两个虚拟机。

   [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

2. 选择或创建资源组，键入虚拟机管理员用户名和密码。
3. 选择“查看 + 创建”，然后选择“创建”。 

   部署可能需要 20 分钟或更长时间才能完成。

## <a name="validate-the-deployment"></a>验证部署

虽然不需 IIS 即可创建应用程序网关，但安装它可以验证 Azure 是否已成功创建应用程序网关。 使用 IIS 测试应用程序网关：

1. 在“概述”页面上查找应用程序网关的公共 IP 地址![记录应用程序网关公共 IP 地址](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)或者，可以选择“所有资源”，在搜索框中输入“myAGPublicIPAddress”，然后在搜索结果中选择该地址 。 Azure 会在“概览”页上显示公共 IP 地址。
2. 复制公共 IP 地址，然后将其粘贴到浏览器的地址栏中，以便浏览该 IP 地址。
3. 检查响应。 有效响应验证应用程序网关是否已成功创建，以及是否能够成功连接后端。

   ![测试应用程序网关](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   多次刷新浏览器就会看到与 myVM1 和 myVM2 的连接。

## <a name="clean-up-resources"></a>清理资源

如果不再需要通过应用程序网关创建的资源，请删除资源组。 这样会删除应用程序网关和所有相关的资源。

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```azurepowershell
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [通过 Azure CLI 使用应用程序网关管理 Web 流量](./tutorial-manage-web-traffic-cli.md)

