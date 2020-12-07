---
title: 在 Azure 专用链接中创建专用终结点
description: 在本快速入门中，将使用 Azure 资源管理器模板（ARM 模板）创建专用终结点。
services: private-link
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
origin.date: 05/26/2020
author: rockboyfor
ms.date: 09/07/2020
ms.testscope: yes|no
ms.testdate: 09/07/2020null
ms.author: v-yeche
ms.openlocfilehash: 63153325cad9b5c321189e303d5a141048c07b2c
ms.sourcegitcommit: a1f565fd202c1b9fd8c74f814baa499bbb4ed4a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96507902"
---
<!--Verified successfully-->
# <a name="quickstart-create-a-private-endpoint-by-using-an-arm-template"></a>快速入门：使用 ARM 模板创建专用终结点

在本快速入门中，将使用 Azure 资源管理器模板（ARM 模板）创建专用终结点。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

还可以使用 [Azure 门户](create-private-endpoint-portal.md)、[Azure PowerShell](create-private-endpoint-powershell.md) 或 [Azure CLI](create-private-endpoint-cli.md) 完成本快速入门。

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署到 Azure":::](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

需要一个具有活动订阅的 Azure 帐户。 [免费创建帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="review-the-template"></a>查看模板

该模板为 Azure SQL 数据库的实例创建专用终结点。

本快速入门中使用的模板来自 [Azure 快速启动模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-private-endpoint-sql/)。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "sqlAdministratorLogin": {
      "type": "string",
      "metadata": {
        "description": "The administrator username of the SQL logical server"
      }
    },
    "sqlAdministratorLoginPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The administrator password of the SQL logical server."
      }
    },
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
    "VmSize": {
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
    "vnetAddressPrefix": "10.0.0.0/16",
    "subnet1Prefix": "10.0.0.0/24",
    "subnet1Name": "mySubnet",
    "sqlServerName": "[concat('sqlserver', uniqueString(resourceGroup().id))]",
    "databaseName": "[concat(variables('sqlServerName'),'/sample-db')]",
    "privateEndpointName": "myPrivateEndpoint",
    "privateDnsZoneName": "privatelink.database.chinacloudapi.cn",
    "pvtendpointdnsgroupname": "[concat(variables('privateEndpointName'),'/mydnsgroupname')]",
    "vmName": "[take(concat('myVm', uniqueString(resourceGroup().id)),15)]",    
    "publicIpAddressName": "[concat(variables('vmName'), 'PublicIP' )]",
    "networkInterfaceName": "[concat(variables('vmName'),'NetInt')]",
    "osDiskType": "Standard_LRS"
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2019-06-01-preview",
      "name": "[variables('sqlServerName')]",
      "location": "[parameters('location')]",
      "tags": {
        "displayName": "[variables('sqlServerName')]"
      },
      "kind": "v12.0",
      "properties": {
        "administratorLogin": "[parameters('sqlAdministratorLogin')]",
        "administratorLoginPassword": "[parameters('sqlAdministratorLoginPassword')]",
        "version": "12.0",
        "publicNetworkAccess": "Disabled"
      },
      "resources": [
      ]
    },
    {
      "type": "Microsoft.Sql/servers/databases",
      "apiVersion": "2019-06-01-preview",
      "name": "[variables('databaseName')]",      
      "location": "[parameters('location')]",
      "tags": {
        "displayName": "[variables('databaseName')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.Sql/servers', variables('sqlServerName'))]"
      ],
      "sku": {
        "name": "Basic",
        "tier": "Basic",
        "capacity": 5
      },
      "properties": {
        "collation": "SQL_Latin1_General_CP1_CI_AS",
        "edition": "Basic",
        "maxSizeBytes": 104857600,
        "requestedServiceObjectiveName": "Basic",
        "sampleName" : "AdventureWorksLT"
      }
    },

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
        }
      },
      "resources": [
        {
          "type": "subnets",
          "apiVersion": "2018-10-01",
          "name": "[variables('subnet1Name')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[variables('vnetName')]"
          ],
          "properties": {
            "addressPrefix": "[variables('subnet1Prefix')]",
            "privateEndpointNetworkPolicies": "Disabled"
          }
        }
      ]
    },
    {
      "type": "Microsoft.Network/privateEndpoints",
      "apiVersion": "2019-04-01",
      "name": "[variables('privateEndpointName')]",
      "location": "[parameters('location')]",      
      "dependsOn": [
        "[variables('vnetName')]",
        "[variables('sqlServerName')]"
      ],
      "properties": {
        "subnet": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('vnetName'), variables('subnet1Name'))]"
        },
        "privateLinkServiceConnections": [
          {
            "name": "[variables('privateEndpointName')]",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.Sql/servers',variables('sqlServerName'))]",
              "groupIds": [
                "sqlServer"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Network/privateDnsZones",
      "apiVersion": "2018-09-01",
      "name": "[variables('privateDnsZoneName')]",
      "location": "global",
      "dependsOn": [
        "[variables('vnetName')]"
      ]
    },
    {
      "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
      "apiVersion": "2018-09-01",
      "name": "[concat(variables('privateDnsZoneName'), '/', variables('privateDnsZoneName'), '-link')]",
      "location": "global",
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateDnsZones', variables('privateDnsZoneName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks', variables('vnetName'))]"
      ],
      "properties": {
        "registrationEnabled": false,
        "virtualNetwork": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks', variables('vnetName'))]"
        }
      }
    },
    {
      "type": "Microsoft.Network/privateEndpoints/privateDnsZoneGroups",
      "apiVersion": "2020-03-01",
      "name": "[variables('pvtendpointdnsgroupname')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateDnsZones', variables('privateDnsZoneName'))]",
        "[variables('privateEndpointName')]"
      ],
      "properties": {
        "privateDnsZoneConfigs": [
          {
            "name": "config1",
            "properties": {
              "privateDnsZoneId": "[resourceId('Microsoft.Network/privateDnsZones', variables('privateDnsZoneName'))]"
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2019-09-01",
      "name": "[variables('publicIpAddressName')]",
      "location": "[parameters('location')]",
      "tags": {
        "displayName": "[variables('publicIpAddressName')]"
      },
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[tolower(variables('vmName'))]"
        }
      }
    },
    {
      "type": "Microsoft.Network/networkInterfaces",
      "apiVersion": "2019-09-01",
      "name": "[variables('networkInterfaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIpAddressName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks', variables('vnetName'))]"
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
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIpAddressName'))]"
              },
              "subnet": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('vnetName'), variables('subnet1Name'))]"
              }
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2019-07-01",
      "name": "[variables('vmName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/networkInterfaces', variables('networkInterfaceName'))]"
      ],
      "tags": {
        "displayName": "[variables('vmName')]"
      },
      "properties": {
        "hardwareProfile": {
          "vmSize": "[parameters('VmSize')]"
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
    }

  ]
}
```

模板中定义了多个 Azure 资源：

<!--Not Available on Azure Global template link-->

- **Microsoft.Sql/servers**：带有示例数据库的 SQL 数据库实例。
- **Microsoft.Sql/servers/databases**：示例数据库。
- **Microsoft.Network/virtualNetworks**：部署专用终结点的虚拟网络。
- **Microsoft.Network/privateEndpoints**：用于访问 SQL 数据库实例的专用终结点。
- **Microsoft.Network/privateDnsZones**：用于解析专用终结点 IP 地址的区域。
- **Microsoft.Network/privateDnsZones/virtualNetworkLinks**
- **Microsoft.Network/privateEndpoints/privateDnsZoneGroups**：用于将专用终结点与专用 DNS 区域相关联的区域组。
- **Microsoft.Network/publicIpAddresses**：用于访问虚拟机的公共 IP 地址。
- **Microsoft.Network/networkInterfaces**：虚拟机的网络接口。
- **Microsoft.Compute/virtualMachines**：用于测试专用终结点与 SQL 数据库实例的专用连接的虚拟机。

## <a name="deploy-the-template"></a>部署模板

下面介绍如何将 ARM 模板部署到 Azure：

1. 若要登录到 Azure 并打开模板，请选择“部署到 Azure”。 该模板创建专用终结点、SQL 数据库实例、网络基础结构和要验证的虚拟机。
    
    > [!NOTE]
    > 必须修改从 GitHub 存储库“azure-quickstart-templates”下载或参考的模板，以适应 Azure 中国云环境。 例如，替换某些终结点（将“blob.core.windows.net”替换为“blob.core.chinacloudapi.cn”，将“cloudapp.azure.com”替换为“chinacloudapp.cn”）；必要时更改某些不受支持的位置、VM 映像、VM 大小、SKU 以及资源提供程序的 API 版本。
    > 
    > 在本文中，在选择“部署到 Azure”之前，在模板的变量部分更改 `privateDnsZoneName` 的属性。
    > 1. 在第 52 行上，更改为 `"privateDnsZoneName": "privatelink.database.chinacloudapi.cn",`。

    
    [:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署到 Azure":::](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. 选择或创建资源组。
3. 键入 SQL 管理员登录名和密码。
4. 键入虚拟机管理员用户名和密码。
5. 阅读条款和条件声明。 如果你同意，请选择“我同意上述条款和条件” > “购买” 。 部署可能需要 20 分钟或更长时间才能完成。

## <a name="validate-the-deployment"></a>验证部署

> [!NOTE]
> ARM 模板为虚拟机 myVm<b>{uniqueid}</b> 资源和 SQL 数据库 sqlserver<b>{uniqueid}</b> 资源生成唯一名称。 用生成的值替换 {uniqueid}。

### <a name="connect-to-a-vm-from-the-internet"></a>从 Internet 连接到 VM

从 Internet 连接到 VM myVm{uniqueid}，如下所示：

1. 在门户的搜索栏中，输入 myVm{uniqueid}。

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

### <a name="access-the-sql-database-server-privately-from-the-vm"></a>以私密方式从 VM 访问 SQL 数据库服务器

下面介绍了如何使用专用终结点从 VM 连接到 SQL 数据库服务器。

1. 在 myVM{uniqueid} 的远程桌面中，打开 PowerShell。
2. 输入以下内容：nslookup sqlserver{uniqueid}.database.chinacloudapi.cn。 
    将收到类似于下面的消息：

    ```
      Server:  UnKnown
      Address:  168.63.129.16
      Non-authoritative answer:
      Name:    sqlserver.privatelink.database.chinacloudapi.cn
      Address:  10.0.0.5
      Aliases:  sqlserver.database.chinacloudapi.cn
    ```

3. 安装 SQL Server Management Studio。
4. 在“连接服务器”中，输入或选择以下信息 **** ：
    - **服务器类型**：选择“数据库引擎”。
    - **服务器名称**：选择“sqlserver{uniqueid}.database.chinacloudapi.cn”。
    - **用户名**：输入在创建过程中提供的用户名。
    - 密码：输入在创建过程中提供的密码。
    - **记住密码**：选择“是”。 ****

5. 选择“连接”。
6. 从左侧菜单中转到“数据库”。
7. （可选）创建或查询 sample-db 中的信息。
8. 关闭与 myVm{uniqueid} 的远程桌面连接。

## <a name="clean-up-resources"></a>清理资源

如果不再需要为专用终结点创建的资源，请删除资源组。 这会删除该专用终结点和所有相关资源。

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```powershell
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>后续步骤

详细了解 [Azure 专用链接](private-link-overview.md)。

<!-- Update_Description: new article about create private endpoint template -->
<!--NEW.date: 09/07/2020-->