---
title: 快速入门：使用 Azure 防火墙管理器保护虚拟中心安全 - 资源管理器模板
description: 本快速入门介绍了如何使用 Azure 防火墙管理器保护虚拟中心安全。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: d68f39b6b94fbfb236b642f809547050145605ec
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105043"
---
# <a name="quickstart-secure-your-virtual-hub-using-azure-firewall-manager---arm-template"></a>快速入门：使用 Azure 防火墙管理器保护虚拟中心安全 - ARM 模板

在本快速入门中，使用 Azure 资源管理器模板（ARM 模板），通过 Azure 防火墙管理器来保护虚拟中心的安全。 部署的防火墙具有允许连接到 `www.microsoft.com` 的应用程序规则。 部署了两个 Windows Server 2019 虚拟机以测试防火墙。 一个跳转服务器用于连接到工作负载服务器。 从工作负载服务器，只能连接到 `www.microsoft.com`。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

有关 Azure 防火墙管理器的详细信息，请参阅[什么是 Azure 防火墙管理器？](overview.md)。

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [创建一个试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="review-the-template"></a>查看模板

此模板使用 Azure 防火墙管理器以及支持该场景所需的资源创建了一个安全虚拟中心。

### <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/fwm-docs-qs/)。

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
       "adminUsername": {
           "type": "String",
           "metadata": {
               "description": "Admin username for the servers"
           }
       },
       "adminPassword": {
           "type": "SecureString",
           "metadata": {
               "description": "Password for the admin account on the servers"
           }
       },
        "location": {
            "defaultValue": "[resourceGroup().location]",
            "type": "String",
            "metadata": {
                "description": "Location for all resources."
            }
        },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D2_v3",
      "metadata": {
        "description": "Size of the virtual machine."
      }
    }
    },
    "resources": [
        {
            "type": "Microsoft.Network/virtualWans",
            "apiVersion": "2019-08-01",
            "name": "VWan-01",
            "location": "[parameters('location')]",
            "properties": {
                "disableVpnEncryption": false,
                "allowBranchToBranchTraffic": true,
                "allowVnetToVnetTraffic": false,
                "office365LocalBreakoutCategory": "None",
                "type": "Standard"
            }
        },
        {
            "type": "Microsoft.Network/virtualHubs",
            "apiVersion": "2020-04-01",
            "name": "Hub-01",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/virtualWans', 'VWan-01')]"
            ],
            "properties": {
                "virtualNetworkConnections": [
                    {
                        "name": "hub-spoke",
                        "properties": {
                            "remoteVirtualNetwork": {
                                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'Spoke-01')]"
                            },
                            "allowHubToRemoteVnetTransit": true,
                            "allowRemoteVnetToUseHubVnetGateways": false,
                            "enableInternetSecurity": true
                        }
                    }
                ],
                "addressPrefix": "10.1.0.0/16",
                "virtualWan": {
                    "id": "[resourceId('Microsoft.Network/virtualWans', 'VWan-01')]"
                },
                "azureFirewall": {
                    "id": "[resourceId('Microsoft.Network/azureFirewalls', 'AzfwTest')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/firewallPolicies",
            "apiVersion": "2019-08-01",
            "name": "Policy-01",
            "location": "[parameters('location')]",
            "properties": {
                "threatIntelMode": "Alert"
            }
        },
        {
            "type": "Microsoft.Network/firewallPolicies/ruleGroups",
            "apiVersion": "2019-08-01",
            "name": "Policy-01/DefaultApplicationRuleCollectionGroup",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/firewallPolicies','Policy-01')]"
                    ],
            "properties": {
                "priority": 300,
                    "rules": [
                            {
                                "name": "RC-01",
                                "priority": 100,
                                "ruleType": "FirewallPolicyFilterRule",
                                "action": {
                                    "type": "Allow"
                                },
                                "ruleConditions": [
                                    {
                                        "name": "Allow-msft",
                                        "protocols": [
                                            {
                                                "protocolType": "http",
                                                "port": 80
                                            },
                                            {
                                                "protocolType": "https",
                                                "port": 443
                                            }
                                        ],
                                        "sourceAddresses": [
                                            "*"
                                        ],
                                        "targetFqdns": [
                                            "*.microsoft.com"
                                        ],
                                        "ruleConditionType": "ApplicationRuleCondition"
                                    }
                                ]
                            }
                        ]
                    }
        },
        {
            "type": "Microsoft.Network/azureFirewalls",
            "apiVersion": "2020-05-01",
            "name": "AzfwTest",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/virtualHubs', 'Hub-01')]",
                "[resourceId('Microsoft.Network/firewallPolicies', 'Policy-01')]"
            ],
            "properties": {
                "sku": {
                    "name": "AZFW_Hub",
                    "tier": "Standard"
                },
                "hubIPAddresses": {
                                    "publicIPs": {
                                        "count": 1
                                    }
                                },
                "virtualHub": {
                    "id": "[resourceId('Microsoft.Network/virtualHubs', 'Hub-01')]"
                },
                "firewallPolicy": {
                    "id": "[resourceId('Microsoft.Network/firewallPolicies', 'Policy-01')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2019-11-01",
            "name": "Spoke-01",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "10.0.0.0/16"
                    ]
                },
                "enableDdosProtection": false,
                "enableVmProtection": false
            }
        },
        {
           "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-11-01",
           "name": "Spoke-01/Workload-SN",
           "dependsOn": [
               "[resourceId('Microsoft.Network/virtualNetworks', 'Spoke-01')]"
           ],
           "properties": {
               "addressPrefix": "10.0.1.0/24",
               "privateEndpointNetworkPolicies": "Enabled",
               "privateLinkServiceNetworkPolicies": "Enabled"
           }
       },
        {
           "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-11-01",
           "name": "Spoke-01/Jump-SN",
           "dependsOn": [
               "[resourceId('Microsoft.Network/virtualNetworks', 'Spoke-01')]",
               "[resourceId('Microsoft.Network/routeTables', 'RT-01')]"
           ],
           "properties": {
               "addressPrefix": "10.0.2.0/24",
                "routeTable": {
                    "id": "[resourceId('Microsoft.Network/routeTables','RT-01')]"
                },
               "privateEndpointNetworkPolicies": "Enabled",
               "privateLinkServiceNetworkPolicies": "Enabled"
           }
       },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2019-07-01",
            "name": "Jump-Srv",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkInterfaces', 'netInterface-jump-srv')]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "2019-Datacenter",
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
                    "computerName": "Jump-Srv",
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
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', 'netInterface-jump-srv')]"
                        }
                    ]
                }
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2019-07-01",
            "name": "Workload-Srv",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkInterfaces', 'netInterface-workload-srv')]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "2019-Datacenter",
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
                    "computerName": "Workload-Srv",
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
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', 'netInterface-workload-srv')]"
                        }
                    ]
                }
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2020-03-01",
            "name": "netInterface-workload-srv",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'Spoke-01','Workload-SN')]",
                "[resourceId('Microsoft.Network/networkSecurityGroups', 'nsg-workload-srv')]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "subnet": {
                                "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'Spoke-01', 'Workload-SN')]"
                            },
                            "primary": true,
                            "privateIPAddressVersion": "IPv4"
                        }
                    }
                ],
                "enableAcceleratedNetworking": false,
                "enableIPForwarding": false,
                "networkSecurityGroup": {
                    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', 'nsg-workload-srv')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2020-03-01",
            "name": "netInterface-jump-srv",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses', 'publicIP-jump-srv')]",
                "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'Spoke-01','Jump-SN')]",
                "[resourceId('Microsoft.Network/networkSecurityGroups', 'nsg-jump-srv')]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'publicIP-jump-srv')]"
                            },
                            "subnet": {
                                "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'Spoke-01', 'Jump-SN')]"
                            },
                            "primary": true,
                            "privateIPAddressVersion": "IPv4"
                        }
                    }
                ],
                "enableAcceleratedNetworking": false,
                "enableIPForwarding": false,
                "networkSecurityGroup": {
                    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', 'nsg-jump-srv')]"
                }
            }
        },
       {
           "type": "Microsoft.Network/networkSecurityGroups",
           "apiVersion": "2020-03-01",
           "name": "nsg-jump-srv",
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
           }
       },
       {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2020-03-01",
            "name": "nsg-workload-srv",
            "location": "[parameters('location')]",
            "properties": {
            }
        },
       {
           "type": "Microsoft.Network/publicIPAddresses",
           "apiVersion": "2019-11-01",
           "name": "publicIP-jump-srv",
           "location": "[parameters('location')]",
           "sku": {
               "name": "Standard"
           },
           "properties": {
               "publicIPAddressVersion": "IPv4",
               "publicIPAllocationMethod": "Static",
               "idleTimeoutInMinutes": 4
           }
       },
        {
            "type": "Microsoft.Network/routeTables",
            "apiVersion": "2020-03-01",
            "name": "RT-01",
            "location": "[parameters('location')]",
            "properties": {
                "disableBgpRoutePropagation": false,
                "routes": [
                    {
                        "name": "jump-to-inet",
                        "properties": {
                            "addressPrefix": "0.0.0.0/0",
                            "nextHopType": "Internet"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/virtualHubs/routeTables",
            "apiVersion": "2019-07-01",
            "name": "Hub-01/VirtualNetworkRouteTable",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/virtualHubs', 'Hub-01')]",
                "[resourceId('Microsoft.Network/azureFirewalls', 'AzfwTest')]"
            ],
            "properties": {
                "routes": [
                {
                   "destinationType": "CIDR",
                        "destinations": [
                            "10.0.1.0/24",
                            "0.0.0.0/0"
                                        ],
                         "nextHopType": "IPAddress",
                            "nextHops": [
                                "10.1.64.4"
                                        ]
                                    }
                 ],
                "attachedConnections": [
                    "All_Vnets"
                ]
            }
        },
        {
            "type": "Microsoft.Network/virtualHubs/routeTables",
            "apiVersion": "2019-07-01",
            "name": "Hub-01/BranchRouteTable",
            "location": "[parameters('location')]",
            "dependsOn": [
                    "[resourceId('Microsoft.Network/virtualHubs', 'Hub-01')]",
                    "[resourceId('Microsoft.Network/azureFirewalls', 'AzfwTest')]",
                    "[resourceId('Microsoft.Network/virtualHubs/routeTables', 'Hub-01','VirtualNetworkRouteTable')]"
            ],
            "properties": {
                    "routes": [
                    {
                        "destinationType": "CIDR",
                           "destinations": [
                               "10.0.1.0/24"
                            ],
                        "nextHopType": "IPAddress",
                            "nextHops": [
                                "10.1.64.4"
                            ]
                    }
                    ],
                        "attachedConnections": [
                           "All_Branches"
```

模板中定义了多个 Azure 资源：

- [**Microsoft.Network/virtualWans**](/azure/templates/microsoft.network/virtualWans)
- [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualHubs)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

## <a name="deploy-the-template"></a>部署模板

将 ARM 模板部署到 Azure：

1. 选择“部署到 Azure”，登录到 Azure 并打开模板。 此模板会创建 Azure 防火墙、虚拟 WAN 和虚拟中心、网络基础结构和两个虚拟机。

   [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

2. 在门户中的“安全虚拟中心”页上，键入或选择以下值：
   - 订阅：从现有订阅中选择 
   - 资源组：从现有资源组中选择，或者选择“新建”，然后选择“确定”。 
   - 位置：选择一个位置
   - 管理员用户名：键入管理员用户帐户的用户名 
   - 管理员密码：键入管理员密码或密钥

3. 选择“查看 + 创建”，然后选择“创建” 。 部署可能需要 10 分钟或更长时间才能完成。

## <a name="validate-the-deployment"></a>验证部署

现在，测试防火墙以确认它可按预期工作。

1. 在 Azure 门户中，查看“Workload-Srv”虚拟机的网络设置并记下专用 IP 地址。
2. 将远程桌面连接到“Jump-Srv”虚拟机，然后登录。 在这里，打开与“Workload-Srv”专用 IP 地址建立的远程桌面连接。

3. 打开 Internet Explorer 并浏览到 `www.microsoft.com` 。
4. 出现 Internet Explorer 安全警报时，请选择“确定” > “关闭”。  

   应会看到 Microsoft 主页。

5. 浏览到 `www.google.com` 。

   防火墙应会阻止你访问。

现已验证防火墙规则可正常工作：

* 可以浏览到一个允许的 FQDN，但不能浏览到其他任何 FQDN。

## <a name="clean-up-resources"></a>清理资源

如果不再需要为防火墙创建的资源，请删除资源组。 这会删除该防火墙和所有相关资源。

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解安全合作伙伴提供程序](trusted-security-partners.md)
