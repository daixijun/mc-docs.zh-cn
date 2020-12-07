---
title: 快速入门：使用资源管理器模板创建 Ubuntu Linux VM
description: 本快速入门介绍如何使用资源管理器模板创建 Linux 虚拟机
author: Johnnytechn
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 12/01/2020
ms.author: v-johya
ms.custom: subject-armqs
ms.openlocfilehash: 47982de4d1e291f57345408844a9a1cdd03af855
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96431623"
---
# <a name="quickstart-create-an-ubuntu-linux-virtual-machine-using-an-arm-template"></a>快速入门：使用 ARM 模板创建 Ubuntu Linux 虚拟机

本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）在 Azure 中部署 Ubuntu Linux 虚拟机 (VM)。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-linux%2fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/)。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "defaultValue": "simpleLinuxVM",
            "metadata": {
                "description": "The name of you Virtual Machine."
            }
        },
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "Username for the Virtual Machine."
            }
        },
        "authenticationType": {
            "type": "string",
            "defaultValue": "password",
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
        "dnsLabelPrefix": {
            "type": "string",
            "defaultValue": "[toLower(concat('simplelinuxvm-', uniqueString(resourceGroup().id)))]",
            "metadata": {
                "description": "Unique DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "ubuntuOSVersion": {
            "type": "string",
            "defaultValue": "18.04-LTS",
            "allowedValues": [
                "12.04.5-LTS",
                "14.04.5-LTS",
                "16.04.0-LTS",
                "18.04-LTS"
            ],
            "metadata": {
                "description": "The Ubuntu version for the VM. This will pick a fully patched image of this given Ubuntu version."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "VmSize": {
            "type": "string",
            "defaultValue": "Standard_B2s",
            "metadata": {
                "description": "The size of the VM"
            }
        },
        "virtualNetworkName": {
            "type": "string",
            "defaultValue": "vNet",
            "metadata": {
                "description": "Name of the VNET"
            }
        },
        "subnetName": {
            "type": "string",
            "defaultValue": "Subnet",
            "metadata": {
                "description": "Name of the subnet in the virtual network"
            }
        },
        "networkSecurityGroupName": {
            "type": "string",
            "defaultValue": "SecGroupNet",
            "metadata": {
                "description": "Name of the Network Security Group"
            }
        }
    },
    "variables": {
        "publicIpAddressName": "[concat(parameters('vmName'), 'PublicIP' )]",
        "networkInterfaceName": "[concat(parameters('vmName'),'NetInt')]",
        "subnetRef": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
        "osDiskType": "Standard_LRS",
        "subnetAddressPrefix": "10.1.0.0/24",
        "addressPrefix": "10.1.0.0/16",
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
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2018-10-01",
            "name": "[variables('networkInterfaceName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups/', parameters('networkSecurityGroupName'))]",
                "[resourceId('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
                "[resourceId('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            },
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIpAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                            }
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[resourceId('Microsoft.Network/networkSecurityGroups',parameters('networkSecurityGroupName'))]"
                }
            }
        },
        {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-02-01",
            "name": "[parameters('networkSecurityGroupName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "SSH",
                        "properties": {
                            "priority": 1000,
                            "protocol": "TCP",
                            "access": "Allow",
                            "direction": "Inbound",
                            "sourceAddressPrefix": "*",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "22"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2019-04-01",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[variables('subnetAddressPrefix')]",
                            "privateEndpointNetworkPolicies": "Enabled",
                            "privateLinkServiceNetworkPolicies": "Enabled"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/publicIpAddresses",
            "apiVersion": "2019-02-01",
            "name": "[variables('publicIpAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIpAllocationMethod": "Dynamic",
                "publicIPAddressVersion": "IPv4",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsLabelPrefix')]"
                },
                "idleTimeoutInMinutes": 4
            },
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2019-03-01",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkInterfaces/', variables('networkInterfaceName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('VmSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[variables('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "[parameters('ubuntuOSVersion')]",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[parameters('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPasswordOrKey')]",
                    "linuxConfiguration": "[if(equals(parameters('authenticationType'), 'password'), json('null'), variables('linuxConfiguration'))]"
                }
            }
        }
    ],
    "outputs": {
        "adminUsername": {
            "type": "string",
            "value": "[parameters('adminUsername')]"
        },
        "hostname": {
            "type": "string",
            "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]"
        },
        "sshCommand": {
            "type": "string",
            "value": "[concat('ssh ', parameters('adminUsername'), '@', reference(variables('publicIPAddressName')).dnsSettings.fqdn)]"
        }
    }
}
```


该模板中定义了多个资源：

- [Microsoft.Network/virtualNetworks/subnets](https://docs.microsoft.com/azure/templates/Microsoft.Network/virtualNetworks/subnets)：创建子网。
- [Microsoft.Storage/storageAccounts](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)：创建存储帐户。
- [Microsoft.Network/networkInterfaces](https://docs.microsoft.com/azure/templates/Microsoft.Network/networkInterfaces)：创建 NIC。
- [Microsoft.Network/networkSecurityGroups](https://docs.microsoft.com/azure/templates/Microsoft.Network/networkSecurityGroups)：创建网络安全组。
- [Microsoft.Network/virtualNetworks](https://docs.microsoft.com/azure/templates/Microsoft.Network/virtualNetworks)：创建虚拟网络。
- [Microsoft.Network/publicIPAddresses](https://docs.microsoft.com/azure/templates/Microsoft.Network/publicIPAddresses)：创建公共 IP 地址。
- [Microsoft.Compute/virtualMachines](https://docs.microsoft.com/azure/templates/Microsoft.Compute/virtualMachines)：创建虚拟机。

## <a name="deploy-the-template"></a>部署模板

1. 选择下图登录到 Azure 并打开一个模板。 该模板将创建 Key Vault 和机密。

    [![部署到 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-linux%2fazuredeploy.json)

1. 选择或输入以下值。 使用可用的默认值。

    - 订阅：选择一个 Azure 订阅。
    - 资源组：从下拉列表中选择现有资源组，或选择“新建”并输入资源组的唯一名称，然后单击“确定” 。
    - 位置：选择一个位置。  例如，**中国北部**。
    - 管理员用户名：提供用户名，如 azureuser。
    - **身份验证类型**：可以在使用 SSH 密钥或密码之间进行选择。
    - 管理员密码或密钥，具体取决于你选择了哪种身份验证类型：
        - 如果选择“密码”，则密码长度必须至少为 12 个字符，且符合[定义的复杂性要求](faq.md#what-are-the-password-requirements-when-creating-a-vm)。
        - 如果选择“sshPublicKey”，则粘贴公钥的内容。
    - DNS 标签前缀：输入要用作 DNS 标签一部分的唯一标识符。
    - Ubuntu OS 版本：选择想要在 VM 上运行的 Ubuntu 版本。
    - 位置：默认与资源组位置相同（如果已存在）。
    - VM 大小：选择要用于 VM 的[大小](../sizes.md)。
    - 虚拟网络名称：要用于 vNet 的名称。
    - 子网名称：VM 应使用的子网的名称。
    - 网络安全组名称：NSG 的名称。
1. 选择“查看 + 创建”。 验证完成后，选择“创建”以创建和部署 VM。


使用 Azure 门户部署模板。 除了 Azure 门户，还可以使用 Azure CLI、Azure PowerShell 和 REST API。 若要了解其他部署方法，请参阅[部署模板](../../azure-resource-manager/templates/deploy-cli.md)。

## <a name="review-deployed-resources"></a>查看已部署的资源

你可以使用 Azure 门户来检查 VM 和创建的其他资源。 部署完成后，请选择“转到资源组”以查看 VM 和其他资源。


## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组，可以将其删除，这将删除 VM 和资源组中的所有资源。 

1. 选择“资源组”。
1. 在资源组页上，选择“删除”。
1. 出现提示时，键入资源组的名称，然后选择“删除”。


## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用 ARM 模板部署了一个简单的虚拟机。 若要详细了解 Azure 虚拟机，请继续学习 Linux VM 的教程。


> [!div class="nextstepaction"]
> [Azure Linux 虚拟机教程](./tutorial-manage-vm.md)

