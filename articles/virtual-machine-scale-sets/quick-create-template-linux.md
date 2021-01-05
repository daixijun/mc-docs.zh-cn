---
title: 快速入门 - 使用 Azure 资源管理器模板创建 Linux 虚拟机规模集
description: 了解如何使用 Azure 资源管理器模板来部署示例应用和配置自动缩放规则，以便快速创建 Linux 虚拟机规模集
author: ju-shim
ms.author: v-junlch
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.subservice: linux
ms.date: 12/28/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, subject-armqs, devx-track-azurecli
ms.openlocfilehash: 84b69641c5e054a93ac8212e1f59c2403c0e17b2
ms.sourcegitcommit: a37f80e7abcf3e42859d6ff73abf566efed783da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97829443"
---
# <a name="quickstart-create-a-linux-virtual-machine-scale-set-with-an-arm-template"></a>快速入门：使用 ARM 模板创建 Linux 虚拟机规模集

利用虚拟机规模集，可以部署和管理一组自动缩放的虚拟机。 可以手动缩放规模集中的 VM 数，也可以定义规则，以便根据资源使用情况（如 CPU 使用率、内存需求或网络流量）进行自动缩放。 然后，Azure 负载均衡器会将流量分配到规模集中的 VM 实例。 本快速入门将使用 Azure 资源管理器模板（ARM 模板）创建虚拟机规模集并部署示例应用程序。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

ARM 模板允许部署相关资源的组。 在单个模板中，可以创建虚拟机规模集、安装应用程序，以及配置自动缩放规则。 在借助变量和参数的情况下，可以重复使用此模板来更新现有的规模集，或者创建更多的规模集。 可通过 Azure 门户、Azure CLI、Azure PowerShell 或持续集成/持续交付 (CI/CD) 管道部署模板。

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-bottle-autoscale%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/201-vmss-bottle-autoscale/)。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for all resources"
        }
      },
      "vmSku": {
        "type": "string",
        "defaultValue": "Standard_D1_v2",
        "metadata": {
          "description": "Size of VMs in the VM Scale Set."
        }
      },
      "vmssName": {
        "type": "string",
        "metadata": {
          "description": "String used as a base for naming resources (9 characters or less). A hash is prepended to this string for some resources, and resource-specific information is appended."
        }
      },
      "instanceCount": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1,
        "maxValue": 100,
        "metadata": {
          "description": "Number of VM instances (100 or less)."
        }
      },
      "adminUsername": {
        "type": "string",
        "metadata": {
          "description": "Admin username on all VMs."
        }
      },
      "authenticationType": {
        "type": "string",
        "defaultValue": "sshPublicKey",
        "allowedValues": [
          "sshPublicKey",
          "password"
        ],
        "metadata": {
          "description": "Type of authentication to use on the Virtual Machine. SSH key is recommended."
        }
      },
      "adminPasswordOrKey": {
        "type": "securestring",
        "metadata": {
          "description": "SSH Key or password for the Virtual Machine. SSH key is recommended."
        }
      },
      "_artifactsLocation": {
        "type": "string",
        "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/",
        "metadata": {
          "description": "The base URI where artifacts required by this template are located"
        }
      },
      "_artifactsLocationSasToken": {
        "type": "securestring",
        "defaultValue": "",
        "metadata": {
          "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated"
        }
      }
    },
    "variables": {
      "addressPrefix": "10.0.0.0/16",
      "subnetPrefix": "10.0.0.0/24",
      "virtualNetworkName": "[concat(parameters('vmssName'), 'vnet')]",
      "publicIPAddressName": "[concat(parameters('vmssName'), 'pip')]",
      "subnetName": "[concat(parameters('vmssName'), 'subnet')]",
      "loadBalancerName": "[concat(parameters('vmssName'), 'lb')]",
      "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]",
      "natPoolName": "[concat(parameters('vmssName'), 'natpool')]",
      "natpool2Name": "[concat(parameters('vmssName'), 'natpool2')]",
      "bePoolName": "[concat(parameters('vmssName'), 'bepool')]",
      "natStartPort": 50000,
      "natEndPort": 50120,
      "natBackendPort": 22,
      "nicName": "[concat(parameters('vmssName'), 'nic')]",
      "ipConfigName": "[concat(parameters('vmssName'), 'ipconfig')]",
      "frontEndIPConfigID": "[resourceId('Microsoft.Network/loadBalancers/frontendIPConfigurations', variables('loadBalancerName'),'loadBalancerFrontEnd')]",
      "osType": {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "16.04-LTS",
        "version": "latest"
      },
      "imageReference": "[variables('osType')]",
      "linuxConfiguration": {
        "disablePasswordAuthentication": true,
        "ssh": {
          "publicKeys": [
            {
              "path": "[concat('/home/', parameters('adminUsername'), '/.ssh/authorized_keys')]",
              "keyData": "[parameters('adminPasswordOrKey')]"
            }
          ]
        }
      }
    },
    "resources": [
      {
        "type": "Microsoft.Network/virtualNetworks",
        "apiVersion": "2020-06-01",
        "name": "[variables('virtualNetworkName')]",
        "location": "[parameters('location')]",
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
        "apiVersion": "2020-06-01",
        "name": "[variables('publicIPAddressName')]",
        "location": "[parameters('location')]",
        "properties": {
          "publicIPAllocationMethod": "Dynamic",
          "dnsSettings": {
            "domainNameLabel": "[parameters('vmssName')]"
          }
        }
      },
      {
        "type": "Microsoft.Network/loadBalancers",
        "apiVersion": "2020-06-01",
        "name": "[variables('loadBalancerName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
          "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))]"
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
            },
            {
              "name": "[variables('natPool2Name')]",
              "properties": {
                "frontendIPConfiguration": {
                  "id": "[variables('frontEndIPConfigID')]"
                },
                "protocol": "Tcp",
                "frontendPortRangeStart": 9000,
                "frontendPortRangeEnd": 9120,
                "backendPort": 9000
              }
            }
          ]
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets",
        "apiVersion": "2020-06-01",
        "name": "[parameters('vmssName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('vmSku')]",
          "tier": "Standard",
          "capacity": "[parameters('instanceCount')]"
        },
        "dependsOn": [
          "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
          "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
        ],
        "properties": {
          "overprovision": "false",
          "upgradePolicy": {
            "mode": "Manual"
          },
          "virtualMachineProfile": {
            "storageProfile": {
              "osDisk": {
                "createOption": "FromImage",
                "caching": "ReadWrite"
              },
              "imageReference": "[variables('imageReference')]"
            },
            "osProfile": {
              "computerNamePrefix": "[parameters('vmssName')]",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPasswordOrKey')]",
              "linuxConfiguration": "[if(equals(parameters('authenticationType'), 'password'), json('null'), variables('linuxConfiguration'))]"
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
                            "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]"
                          },
                          "loadBalancerBackendAddressPools": [
                            {
                              "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', variables('loadBalancerName'), variables('bePoolName'))]"
                            }
                          ],
                          "loadBalancerInboundNatPools": [
                            {
                              "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', variables('loadBalancerName'), variables('natPoolName'))]"
                            },
                            {
                              "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', variables('loadBalancerName'), variables('natpool2Name'))]"
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
                  "name": "lapextension",
                  "properties": {
                    "publisher": "Microsoft.Azure.Extensions",
                    "type": "CustomScript",
                    "typeHandlerVersion": "2.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                      "fileUris": [
                        "[uri(parameters('_artifactsLocation'), concat('installserver.sh', parameters('_artifactsLocationSasToken')))]",
                        "[uri(parameters('_artifactsLocation'), concat('workserver.py', parameters('_artifactsLocationSasToken')))]"
                      ],
                      "commandToExecute": "bash installserver.sh"
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
          "[resourceId('Microsoft.Compute/virtualMachineScaleSets', parameters('vmSSName'))]"
        ],
        "properties": {
          "name": "autoscalehost",
          "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', parameters('vmSSName'))]",
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
                    "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', parameters('vmSSName'))]",
                    "timeGrain": "PT1M",
                    "statistic": "Average",
                    "timeWindow": "PT5M",
                    "timeAggregation": "Average",
                    "operator": "GreaterThan",
                    "threshold": 60
                  },
                  "scaleAction": {
                    "direction": "Increase",
                    "type": "ChangeCount",
                    "value": "1",
                    "cooldown": "PT1M"
                  }
                },
                {
                  "metricTrigger": {
                    "metricName": "Percentage CPU",
                    "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', parameters('vmSSName'))]",
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
                    "cooldown": "PT1M"
                  }
                }
              ]
            }
          ]
        }
      }
    ]
  }
  
```

该模板中定义了以下资源：

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
| imageReference               | 用于 VM 实例的平台或自定义映像 | Canonical Ubuntu Server 16.04-LTS         |
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

模板使用自定义脚本扩展来安装 [Bottle](https://bottlepy.org/docs/dev/)（Python Web 框架）和简单的 HTTP 服务器。

两个脚本在 **fileUris** - （*installserver.sh* 和 *workserver.py*）中定义。 从 GitHub 下载这些文件以后，*commandToExecute* 就可以运行 `bash installserver.sh` 来安装和配置应用。

## <a name="deploy-the-template"></a>部署模板

可以通过选择下面的“部署到 Azure”按钮来部署模板。 此按钮可打开 Azure 门户、加载完整的模板，以及提示输入一些参数，例如规模集名称、实例计数和管理员凭据。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-bottle-autoscale%2Fazuredeploy.json)

还可以使用 Azure CLI 部署资源管理器模板：

```azurecli
# Create a resource group
az group create --name myResourceGroup --location ChinaNorth2

# Deploy template into resource group
az deployment group create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

响应提示，为 VM 实例提供规模集名称、实例计数和管理员凭据。 创建规模集和支持资源需要数分钟。

## <a name="validate-the-deployment"></a>验证部署

若要查看正在运行的规模集，请在 Web 浏览器中访问示例 Web 应用程序。 使用 [az network public-ip list](/cli/network/public-ip) 命令获取负载均衡器的公共 IP 地址，如下所示：

```azurecli
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

以“http:\//publicIpAddress:9000/do_work” 格式将负载均衡器的公共 IP 地址输入到 Web 浏览器中。 负载均衡器将流量分发到某个 VM 实例，如以下示例所示：

![NGINX 中的默认网页](./media/virtual-machine-scale-sets-create-template/running-python-app.png)

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、规模集和所有相关的资源，可以使用 [az group delete](/cli/group) 命令将其删除，如下所示。 `--no-wait` 参数会使光标返回提示符处，不会等待操作完成。 `--yes` 参数将确认是否希望删除资源，不会显示询问是否删除的额外提示。

```azurecli
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用 ARM 模板创建了 Linux 规模集，并已使用自定义脚本扩展在 VM 实例上安装了基本的 Python Web 服务器。 若要了解详细信息，请继续学习有关如何创建和管理 Azure 虚拟机规模集的教程。

> [!div class="nextstepaction"]
> [创建和管理 Azure 虚拟机规模集](tutorial-create-and-manage-cli.md)

