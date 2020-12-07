---
title: 在 Azure 专用链接中创建专用链接服务
description: 在本快速入门中，将使用 Azure 资源管理器模板（ARM 模板）创建专用链接服务。
services: private-link
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
origin.date: 05/29/2020
author: rockboyfor
ms.date: 09/07/2020
ms.testscope: no
ms.testdate: 09/07/2020
ms.author: v-yeche
ms.openlocfilehash: bdccdc7f8b2e80a36293a186b41f9c1c4c2a2963
ms.sourcegitcommit: a1f565fd202c1b9fd8c74f814baa499bbb4ed4a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96508016"
---
<!--Verified successfully-->
# <a name="quickstart-create-a-private-link-service-by-using-an-arm-template"></a>快速入门：使用 ARM 模板创建专用链接服务

在本快速入门中，将使用 Azure 资源管理器模板（ARM 模板）创建专用链接服务。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

还可以使用 [Azure 门户](create-private-link-service-portal.md)、[Azure PowerShell](create-private-link-service-powershell.md) 或 [Azure CLI](create-private-link-service-cli.md) 完成本快速入门。

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署到 Azure":::](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

需要一个具有活动订阅的 Azure 帐户。 [免费创建帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="review-the-template"></a>查看模板

此模板创建专用链接服务。

本快速入门中使用的模板来自 [Azure 快速启动模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-privatelink-service/)。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmAdminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "vmAdminPassword": {
      "type": "secureString",
      "metadata": {
        "description": "Password for the Virtual Machine. The password must be at least 12 characters long and have lower case, upper characters, digit and a special character (Regex match)"
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D2_v2",
      "metadata": {
        "description": "The size of the VM"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "vnetName": "myVirtualNetwork",
    "vnetConsumerName": "myPEVnet",
    "vnetAddressPrefix": "10.0.0.0/16",
    "frontendSubnetPrefix": "10.0.1.0/24",
    "frontendSubnetName": "frontendSubnet",
    "backendSubnetPrefix": "10.0.2.0/24",
    "backendSubnetName": "backendSubnet",
    "consumerSubnetPrefix": "10.0.0.0/24",
    "consumerSubnetName": "myPESubnet",
    "loadbalancername": "myILB",
    "backendpoolname": "myBackEndPool",
    "loadBalancerFrontEndIpConfigurationName": "myFrontEnd",
    "healthProbeName": "myHealthProbe",
    "privateEndpointName": "myPrivateEndpoint",
    "vmName": "[take(concat('myVm', uniqueString(resourceGroup().id)),15)]",
    "networkInterfaceName": "[concat(variables('vmName'),'NetInt')]",
    "vmConsumerName": "[take(concat('myConsumerVm', uniqueString(resourceGroup().id)),15)]",
    "publicIpAddressConsumerName": "[concat(variables('vmConsumerName'), 'PublicIP' )]",
    "networkInterfaceConsumerName": "[concat(variables('vmConsumerName'),'NetInt')]",
    "osDiskType": "Standard_LRS",
    "privatelinkservicename": "myPLS",
    "loadbalancerId": "[resourceId('Microsoft.Network/loadBalancers', variables('loadbalancername'))]"

  },
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2018-10-01",
      "name": "[variables('vnetName')]",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetAddressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('frontendSubnetName')]",
            "properties": {
              "addressPrefix": "[variables('frontendSubnetPrefix')]",
              "privateLinkServiceNetworkPolicies": "Disabled"
            }
          },
          {
            "name": "[variables('backendSubnetName')]",
            "properties": {
              "addressPrefix": "[variables('backendSubnetPrefix')]"
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2019-06-01",
      "name": "[variables('loadbalancername')]",
      "type": "Microsoft.Network/loadBalancers",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "dependsOn": [
        "[variables('vnetName')]"
      ],
      "properties": {
        "frontendIPConfigurations": [
          {
            "name": "[variables('loadBalancerFrontEndIpConfigurationName')]",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "subnet": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('vnetName'), variables('frontendSubnetName'))]"
              }
            }
          }
        ],
        "backendAddressPools": [
          {
            "name": "[variables('backendpoolname')]"
          }
        ],
        "inboundNatRules": [
          {
            "name": "RDP-VM0",
            "properties": {
              "frontendIPConfiguration": {
                "id": "[resourceId('Microsoft.Network/loadBalancers/frontendIpConfigurations', variables('loadbalancername'), variables('loadBalancerFrontEndIpConfigurationName'))]"
              },
              "protocol": "Tcp",
              "frontendPort": 3389,
              "backendPort": 3389,
              "enableFloatingIP": false
            }
          }
        ],
        "loadBalancingRules": [
          {
            "Name": "myHTTPRule",
            "properties": {
              "frontendIPConfiguration": {
                "id": "[resourceId('Microsoft.Network/loadBalancers/frontendIpConfigurations', variables('loadbalancername'), variables('loadBalancerFrontEndIpConfigurationName'))]"
              },
              "backendAddressPool": {
                "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', variables('loadbalancername'), variables('backendpoolname'))]"
              },
              "probe": {
                "id": "[resourceId('Microsoft.Network/loadBalancers/probes', variables('loadBalancerName'), variables('healthProbeName'))]"
              },
              "protocol": "Tcp",
              "frontendPort": 80,
              "backendPort": 80,
              "idleTimeoutInMinutes": 15
            }
          }
        ],
        "probes": [
          {
            "properties": {
              "protocol": "Tcp",
              "port": 80,
              "intervalInSeconds": 15,
              "numberOfProbes": 2
            },
            "name": "[variables('healthProbeName')]"
          }
        ]
      }
    },
    {
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('networkInterfaceName')]",
      "apiVersion": "2019-09-01",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[variables('loadbalancername')]"
      ],
      "tags": {
        "displayName": "[variables('networkInterfaceName')]"
      },
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipConfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "subnet": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('vnetName'), variables('backendSubnetName'))]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', variables('loadbalancername'), variables('backendpoolname'))]"
                }
              ],
              "loadBalancerInboundNatRules": [
                {
                  "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatRules/', variables('loadbalancername'), 'RDP-VM0')]"
                }
              ]
            }
          }
        ]
      }
    },
    {
      "name": "[variables('vmName')]",
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2019-07-01",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/networkInterfaces', variables('networkInterfaceName'))]"
      ],
      "tags": {
        "displayName": "[variables('vmName')]"
      },
      "properties": {
        "hardwareProfile": {
          "vmSize": "[parameters('vmSize')]"
        },
        "osProfile": {
          "computerName": "[variables('vmName')]",
          "adminUsername": "[parameters('vmAdminUsername')]",
          "adminPassword": "[parameters('vmAdminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2019-Datacenter",
            "version": "latest"
          },
          "osDisk": {
            "name": "[concat(variables('vmName'),'OsDisk')]",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "managedDisk": {
              "storageAccountType": "[variables('osDiskType')]"
            },
            "diskSizeGB": 128
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('networkInterfaceName'))]"
            }
          ]
        }
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(variables('vmName'),'/installcustomscript')]",
      "apiVersion": "2019-03-01",
      "location": "[parameters('location')]",
      "tags": {
        "displayName": "install software for Windows VM"
      },
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines', variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.9",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
          "commandToExecute": "powershell -ExecutionPolicy Unrestricted Install-WindowsFeature -Name Web-Server"
        }
      }
    },
    {
      "type": "Microsoft.Network/privateLinkServices",
      "apiVersion": "2019-04-01",
      "name": "[variables('privatelinkservicename')]",
      "location": "[parameters('location')]",
      "properties": {
        "enableProxyProtocol": false,
        "loadBalancerFrontendIpConfigurations": [
          {
            "id": "[resourceId('Microsoft.Network/loadBalancers/frontendIpConfigurations', variables('loadbalancername'), variables('loadBalancerFrontEndIpConfigurationName'))]"
          }
        ],
        "ipConfigurations": [
          {
            "name": "snet-provider-default-1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "privateIPAddressVersion": "IPv4",
              "subnet": {
                "id": "[reference(variables('loadbalancerId'), '2019-06-01').frontendIPConfigurations[0].properties.subnet.id]"
              },
              "primary": false
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2018-10-01",
      "name": "[variables('vnetConsumerName')]",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetAddressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('consumerSubnetName')]",
            "properties": {
              "addressPrefix": "[variables('consumerSubnetPrefix')]",
              "privateEndpointNetworkPolicies": "Disabled"
            }
          },
          {
            "name": "[variables('backendSubnetName')]",
            "properties": {
              "addressPrefix": "[variables('backendSubnetPrefix')]"
            }
          }
        ]
      }
    },
    {
      "name": "[variables('publicIpAddressConsumerName')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2019-09-01",
      "location": "[parameters('location')]",
      "tags": {
        "displayName": "[variables('publicIpAddressConsumerName')]"
      },
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[tolower(variables('vmConsumerName'))]"
        }
      }
    },
    {
      "name": "[variables('networkInterfaceConsumerName')]",
      "type": "Microsoft.Network/networkInterfaces",
      "apiVersion": "2019-09-01",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[variables('vnetConsumerName')]",
        "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIpAddressConsumerName'))]"
      ],
      "tags": {
        "displayName": "[variables('networkInterfaceConsumerName')]"
      },
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipConfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIpAddressConsumerName'))]"
              },
              "subnet": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('vnetConsumerName'), variables('consumerSubnetName'))]"
              }
            }
          }
        ]
      }
    },
    {
      "name": "[variables('vmConsumerName')]",
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2019-07-01",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/networkInterfaces', variables('networkInterfaceConsumerName'))]"
      ],
      "tags": {
        "displayName": "[variables('vmConsumerName')]"
      },
      "properties": {
        "hardwareProfile": {
          "vmSize": "[parameters('vmSize')]"
        },
        "osProfile": {
          "computerName": "[variables('vmConsumerName')]",
          "adminUsername": "[parameters('vmAdminUsername')]",
          "adminPassword": "[parameters('vmAdminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2019-Datacenter",
            "version": "latest"
          },
          "osDisk": {
            "name": "[concat(variables('vmConsumerName'),'OsDisk')]",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "managedDisk": {
              "storageAccountType": "[variables('osDiskType')]"
            },
            "diskSizeGB": 128
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('networkInterfaceConsumerName'))]"
            }
          ]
        }
      }
    },
    {
      "type": "Microsoft.Network/privateEndpoints",
      "apiVersion": "2019-04-01",
      "name": "[variables('privateEndpointName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[variables('vnetConsumerName')]",
        "[variables('privatelinkservicename')]"
      ],
      "properties": {
        "subnet": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('vnetConsumerName'), variables('consumerSubnetName'))]"
        },
        "privateLinkServiceConnections": [
          {
            "name": "[variables('privateEndpointName')]",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.Network/privateLinkServices',variables('privatelinkservicename'))]"
            }
          }
        ]
      }
    }
  ]
}
```

模板中定义了多个 Azure 资源：

<!--Not Available on Global Azure template link-->

- **Microsoft.Network/virtualNetworks**：每个虚拟机都有一个虚拟网络。
- **Microsoft.Network/loadBalancers**：公开托管服务的虚拟机的负载均衡器。
- **Microsoft.Network/networkInterfaces**：有两个网络接口，每个虚拟机对应一个接口。
- **Microsoft.Compute/virtualMachines**：有两台虚拟机，一台用于托管服务，另一台用于测试到专用终结点的连接。
- **Microsoft.Compute/virtualMachines/extensions**：安装 Web 服务器的扩展。
- **Microsoft.Network/privateLinkServices**：用于公开服务的专用链接服务。
- **Microsoft.Network/publicIpAddresses**：有两个公共 IP 地址，每个虚拟机对应一个地址。
- **Microsoft.Network/privateendpoints**：用于访问服务的专用终结点。

## <a name="deploy-the-template"></a>部署模板

下面介绍如何将 ARM 模板部署到 Azure：

1. 若要登录到 Azure 并打开模板，请选择“部署到 Azure”。 此模板创建虚拟机、标准负载均衡器、专用链接服务、专用终结点、网络和要验证的虚拟机。

    [:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署到 Azure":::](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

2. 选择或创建资源组。
3. 键入虚拟机管理员用户名和密码。
4. 阅读条款和条件声明。 如果你同意，请选择“我同意上述条款和条件” > “购买” 。

## <a name="validate-the-deployment"></a>验证部署

> [!NOTE]
> ARM 模板为虚拟机 myConsumerVm<b>{uniqueid}</b> 资源生成唯一名称。 用生成的值替换 {uniqueid}。

### <a name="connect-to-a-vm-from-the-internet"></a>从 Internet 连接到 VM

从 Internet 连接到 VM myConsumerVm{uniqueid}，如下所示：

1. 在门户的搜索栏中，输入 myConsumerVm{uniqueid}。

2. 选择“连接”。 “连接到虚拟机”随即打开。

3. 选择“下载 RDP 文件”。 Azure 会创建远程桌面协议 ( _.rdp_) 文件，并将其下载到计算机。

4. 打开下载的 .rdp 文件。

    a. 出现提示时，选择“连接”。

    b. 输入创建 VM 时指定的用户名和密码。

    > [!NOTE]
    > 可能需要选择“更多选择” > “使用其他帐户”，以指定在创建 VM 时输入的凭据。

5. 选择“确定”。

6. 你可能会在登录过程中收到证书警告。 如果收到证书警告，请选择“确定”或“继续” 。

7. VM 桌面出现后，将其最小化以返回到本地桌面。

### <a name="access-the-http-service-privately-from-the-vm"></a>以非公开方式从 VM 访问 http 服务

下面介绍了如何使用专用终结点从 VM 连接到 http 服务。

1. 转到 myConsumerVm{uniqueid} 的远程桌面。
2. 打开浏览器并输入专用终结点地址： `http://10.0.0.5/` 。
3. 默认 IIS 页随即出现。

## <a name="clean-up-resources"></a>清理资源

如果不再需要使用专用链接服务创建的资源，请删除资源组。 这会删除专用链接服务和所有相关资源。

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```powershell
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>后续步骤

详细了解 [Azure 专用链接](private-link-overview.md)。

<!-- Update_Description: new article about create private link service template -->
<!--NEW.date: 09/07/2020-->