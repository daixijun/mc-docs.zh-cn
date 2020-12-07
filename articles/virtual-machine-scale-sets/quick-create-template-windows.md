---
title: 快速入门 - 使用 Azure 模板创建 Windows 虚拟机规模集
description: 了解如何使用 Azure 资源管理器模板来部署示例应用和配置自动缩放规则，以便快速创建 Windows 虚拟机规模集
author: ju-shim
ms.author: v-junlch
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.subservice: windows
ms.date: 08/31/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, subject-armqs
ms.openlocfilehash: c58fc8f13da6183ff9475407135e3a63927c4908
ms.sourcegitcommit: f436acd1e2a0108918a6d2ee9a1aac88827d6e37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509096"
---
# <a name="quickstart-create-a-windows-virtual-machine-scale-set-with-an-arm-template"></a>快速入门：使用 ARM 模板创建 Windows 虚拟机规模集

利用虚拟机规模集，可以部署和管理一组自动缩放的虚拟机。 可以手动缩放规模集中的 VM 数，也可以定义规则，以便根据资源使用情况（如 CPU 使用率、内存需求或网络流量）进行自动缩放。 然后，Azure 负载均衡器会将流量分配到规模集中的 VM 实例。 本快速入门将使用 Azure 资源管理器模板（ARM 模板）创建虚拟机规模集并部署示例应用程序。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

ARM 模板允许部署相关资源的组。 在单个模板中，可以创建虚拟机规模集、安装应用程序，以及配置自动缩放规则。 在借助变量和参数的情况下，可以重复使用此模板来更新现有的规模集，或者创建更多的规模集。 可通过 Azure 门户、Azure CLI、Azure PowerShell 或持续集成/持续交付 (CI/CD) 管道部署模板。

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/201-vmss-windows-webapp-dsc-autoscale/)。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmSku": {
      "type": "string",
      "defaultValue": "Standard_A1",
      "metadata": {
        "description": "Size of VMs in the VM Scale Set."
      }
    },
    "windowsOSVersion": {
      "type": "string",
      "defaultValue": "2016-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter",
        "2016-Datacenter"
      ],
      "metadata": {
        "description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version. Allowed values: 2008-R2-SP1, 2012-Datacenter, 2012-R2-Datacenter & 2016-Datacenter."
      }
    },
    "vmssName": {
      "type": "string",
      "metadata": {
        "description": "String used as a base for naming resources. Must be 3-61 characters in length and globally unique across Azure. A hash is prepended to this string for some resources, and resource-specific information is appended."
      },
      "minLength": 3,
      "maxLength": 61
    },
    "instanceCount": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Number of VM instances (100 or less)."
      },
      "maxValue": 100
    },
    "adminUsername": {
      "type": "string",
      "defaultValue": "vmssadmin",
      "metadata": {
        "description": "Admin username on all VMs."
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Admin password on all VMs."
      }
    },
    "_artifactsLocation": {
      "type": "string",
      "metadata": {
        "description": "The base URI where artifacts required by this template are located. For example, if stored on a public GitHub repo, you'd use the following URI: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale."
      },
      "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale"
    },
    "_artifactsLocationSasToken": {
      "type": "securestring",
      "defaultValue": "",
      "metadata": {
        "description": "The sasToken required to access _artifactsLocation.  If your artifacts are stored on a public repo or public storage account you can leave this blank."
      }
    },
    "powershelldscZip": {
      "type": "string",
      "defaultValue": "/DSC/IISInstall.ps1.zip",
      "metadata": {
        "description": "Location of the PowerShell DSC zip file relative to the URI specified in the _artifactsLocation, i.e. /DSC/IISInstall.ps1.zip"
      }
    },
    "webDeployPackage": {
      "type": "string",
      "defaultValue": "/WebDeploy/DefaultASPWebApp.v1.0.zip",
      "metadata": {
        "description": "Location of the  of the WebDeploy package zip file relative to the URI specified in _artifactsLocation, i.e. /WebDeploy/DefaultASPWebApp.v1.0.zip"
      }
    },
    "powershelldscUpdateTagVersion": {
      "type": "string",
      "defaultValue": "1.0",
      "metadata": {
        "description": "Version number of the DSC deployment. Changing this value on subsequent deployments will trigger the extension to run."
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
    "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
    "longNamingInfix": "[toLower(parameters('vmssName'))]",
    "addressPrefix": "10.0.0.0/16",
    "subnetPrefix": "10.0.0.0/24",
    "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
    "publicIPAddressName": "[concat(variables('namingInfix'), 'pip')]",
    "subnetName": "[concat(variables('namingInfix'), 'subnet')]",
    "loadBalancerName": "[concat(variables('namingInfix'), 'lb')]",
    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]",
    "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]",
    "natPoolName": "[concat(variables('namingInfix'), 'natpool')]",
    "bePoolName": "[concat(variables('namingInfix'), 'bepool')]",
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
    "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/',variables('bePoolName'))]",
    "natStartPort": 50000,
    "natEndPort": 50119,
    "natBackendPort": 3389,
    "nicName": "[concat(variables('namingInfix'), 'nic')]",
    "ipConfigName": "[concat(variables('namingInfix'), 'ipconfig')]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
    "osType": {
      "publisher": "MicrosoftWindowsServer",
      "offer": "WindowsServer",
      "sku": "[parameters('windowsOSVersion')]",
      "version": "latest"
    },
    "imageReference": "[variables('osType')]",
    "webDeployPackageFullPath": "[concat(parameters('_artifactsLocation'), parameters('webDeployPackage'))]",
    "powershelldscZipFullPath": "[concat(parameters('_artifactsLocation'), parameters('powershelldscZip'))]",
    "powershelldscArchiveFolder": "DSC",
    "powershelldscArchiveFileName": "IISInstall.ps1.zip"
  },
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2016-03-30",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[variables('subnetPrefix')]"
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2016-03-30",
      "properties": {
        "publicIPAllocationMethod": "Static",
        "dnsSettings": {
          "domainNameLabel": "[variables('longNamingInfix')]"
        }
      }
    },
    {
      "type": "Microsoft.Network/loadBalancers",
      "name": "[variables('loadBalancerName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2016-03-30",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]"
      ],
      "properties": {
        "frontendIPConfigurations": [
          {
            "name": "LoadBalancerFrontEnd",
            "properties": {
              "publicIPAddress": {
                "id": "[variables('publicIPAddressID')]"
              }
            }
          }
        ],
        "backendAddressPools": [
          {
            "name": "[variables('bePoolName')]"
          }
        ],
        "inboundNatPools": [
          {
            "name": "[variables('natPoolName')]",
            "properties": {
              "frontendIPConfiguration": {
                "id": "[variables('frontEndIPConfigID')]"
              },
              "protocol": "Tcp",
              "frontendPortRangeStart": "[variables('natStartPort')]",
              "frontendPortRangeEnd": "[variables('natEndPort')]",
              "backendPort": "[variables('natBackendPort')]"
            }
          }
        ],
        "loadBalancingRules": [
          {
            "name": "LBRule",
            "properties": {
              "frontendIPConfiguration": {
                "id": "[variables('frontEndIPConfigID')]"
              },
              "backendAddressPool": {
                "id": "[variables('lbPoolID')]"
              },
              "protocol": "Tcp",
              "frontendPort": 80,
              "backendPort": 80,
              "enableFloatingIP": false,
              "idleTimeoutInMinutes": 5,
              "probe": {
                "id": "[variables('lbProbeID')]"
              }
            }
          }
        ],
        "probes": [
          {
            "name": "tcpProbe",
            "properties": {
              "protocol": "Tcp",
              "port": 80,
              "intervalInSeconds": 5,
              "numberOfProbes": 2
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('namingInfix')]",
      "location": "[parameters('location')]",
      "apiVersion": "2016-04-30-preview",
      "dependsOn": [
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      "sku": {
        "name": "[parameters('vmSku')]",
        "tier": "Standard",
        "capacity": "[parameters('instanceCount')]"
      },
      "properties": {
        "overprovision": "true",
        "upgradePolicy": {
          "mode": "Automatic"
        },
        "virtualMachineProfile": {
          "storageProfile": {
            "osDisk": {
              "caching": "ReadWrite",
              "createOption": "FromImage"
            },
            "imageReference": "[variables('imageReference')]"
          },
          "osProfile": {
            "computerNamePrefix": "[variables('namingInfix')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
          },
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[variables('nicName')]",
                "properties": {
                  "primary": true,
                  "ipConfigurations": [
                    {
                      "name": "[variables('ipConfigName')]",
                      "properties": {
                        "subnet": {
                          "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'), '/subnets/', variables('subnetName'))]"
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('loadBalancerName'), '/backendAddressPools/', variables('bePoolName'))]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('loadBalancerName'), '/inboundNatPools/', variables('natPoolName'))]"
                          }
                        ]
                      }
                    }
                  ]
                }
              }
            ]
          },
          "extensionProfile": {
            "extensions": [
              {
                "name": "Microsoft.Powershell.DSC",
                "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.9",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('powershelldscUpdateTagVersion')]",
                  "settings": {
                    "configuration": {
                      "url": "[variables('powershelldscZipFullPath')]",
                      "script": "IISInstall.ps1",
                      "function": "InstallIIS"
                    },
                    "configurationArguments": {
                      "nodeName": "localhost",
                      "WebDeployPackagePath": "[variables('webDeployPackageFullPath')]"
                    }
                  }
                }
              }
            ]
          }
        }
      }
    },
    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "autoscalehost",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/', variables('namingInfix'))]"
      ],
      "properties": {
        "name": "autoscalehost",
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('namingInfix'))]",
        "enabled": true,
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('namingInfix'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 50
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('namingInfix'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 30
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ]
      }
    }
  ],
  "outputs": {
    "applicationUrl": {
      "type": "string",
      "value": "[concat('http://', reference(variables('publicIPAddressName')).dnsSettings.fqdn, '/MyApp')]"
    }
  }
}
```

这些模板中定义了这些资源：

- [**Microsoft.Network/virtualNetworks**](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/loadBalancers**](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Compute/virtualMachineScaleSets**](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachinescalesets)
- [**Microsoft.Insights/autoscaleSettings**](https://docs.microsoft.com/azure/templates/microsoft.insights/autoscalesettings)

### <a name="define-a-scale-set"></a>定义规模集

若要使用模板创建规模集，请定义相应的资源。 虚拟机规模集资源类型的核心部件包括：

| 属性                     | 属性说明                                  | 示例模板值                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | 要创建的 Azure 资源类型                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | 规模集名称                                       | myScaleSet                                |
| location                     | 要创建规模集的位置                     | 中国北部 2                                  |
| sku.name                     | 每个规模集实例的 VM 大小                  | Standard_A1                               |
| sku.capacity                 | 一开始需要创建的 VM 实例数           | 2                                         |
| upgradePolicy.mode           | 更改发生时的 VM 实例升级模式              | 自动                                 |
| imageReference               | 用于 VM 实例的平台或自定义映像 | Microsoft Windows Server 2016 Datacenter  |
| osProfile.computerNamePrefix | 每个 VM 实例的名称前缀                     | myvmss                                    |
| osProfile.adminUsername      | 每个 VM 实例的用户名                        | azureuser                                 |
| osProfile.adminPassword      | 每个 VM 实例的密码                        | P@ssw0rd!                                 |

若要自定义规模集模板，可以更改 VM 大小或初始容量。 另一选项是使用其他平台或自定义映像。

### <a name="add-a-sample-application"></a>添加示例应用程序

若要测试规模集，请安装一个基本的 Web 应用程序。 部署规模集时，可以通过 VM 扩展来完成部署后配置和自动化任务，例如安装某个应用。 可以从 Azure 存储或 GitHub 下载脚本，或者在扩展运行时将脚本提供给 Azure 门户。 若要对规模集应用某个扩展，请将 *extensionProfile* 节添加到前面的资源示例中。 扩展配置文件通常定义以下属性：

- 扩展类型
- 扩展发布者
- 扩展版本
- 配置或安装脚本的位置
- 可在 VM 实例上执行的命令

此模板使用 PowerShell DSC 扩展来安装在 IIS 中运行的 ASP.NET MVC 应用。

安装脚本从 url 中定义的 GitHub 下载。 扩展然后运行 *IISInstall.ps1* 脚本中的 *InstallIIS*（在 *function* 和 *Script* 中定义）。 ASP.NET 应用本身作为 Web 部署包提供，该包也从 *WebDeployPackagePath* 中定义的 GitHub 下载：

## <a name="deploy-the-template"></a>部署模板

可以通过选择“部署到 Azure”按钮来部署模板。 此按钮可打开 Azure 门户、加载完整的模板，以及提示输入一些参数，例如规模集名称、实例计数和管理员凭据。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

还可以使用 Azure PowerShell 部署资源管理器模板：

```azurepowershell
# Create a resource group
New-AzResourceGroup -Name myResourceGroup -Location ChinaNorth2

# Deploy template into resource group
New-AzResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateURI https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json

# Update the scale set and apply the extension
Update-AzVmss `
    -ResourceGroupName myResourceGroup `
    -VmScaleSetName myVMSS `
    -VirtualMachineScaleSet $vmssConfig
```

响应提示，为 VM 实例提供规模集名称和管理员凭据。 创建规模集并通过其来应用可配置应用的扩展可能需要 10-15 分钟。

## <a name="validate-the-deployment"></a>验证部署

若要查看正在运行的规模集，请在 Web 浏览器中访问示例 Web 应用程序。 使用 [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) 获取负载均衡器的公共 IP 地址，如下所示：

```azurepowershell
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

以“http:\//publicIpAddress/MyApp”格式将负载均衡器的公共 IP 地址输入到 Web 浏览器中。 负载均衡器将流量分发到某个 VM 实例，如以下示例所示：

![运行 IIS 网站](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、规模集，可以使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) 命令将其删除。 `-Force` 参数将确认是否希望删除资源，不会显示询问是否删除的额外提示。 `-AsJob` 参数会使光标返回提示符处，不会等待操作完成。

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用 ARM 模板创建了 Windows 规模集，并已使用 PowerShell DSC 扩展在 VM 实例上安装了基本的 ASP.NET 应用。 若要了解详细信息，请继续学习有关如何创建和管理 Azure 虚拟机规模集的教程。

> [!div class="nextstepaction"]
> [创建和管理 Azure 虚拟机规模集](tutorial-create-and-manage-powershell.md)

