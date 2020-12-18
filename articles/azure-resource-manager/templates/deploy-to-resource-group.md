---
title: 将资源部署到资源组
description: 介绍如何在 Azure 资源管理器模板中部署资源。 它介绍如何将多个资源组作为目标。
ms.topic: conceptual
origin.date: 11/24/2020
author: rockboyfor
ms.date: 12/14/2020
ms.testscope: yes|no
ms.testdate: 11/23/2020null
ms.author: v-yeche
ms.openlocfilehash: bedd05aad8812b6a310751f308e637c23090cb2c
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97003743"
---
<!--Verified successfully form rename and merge files.-->
# <a name="resource-group-deployments-with-arm-templates"></a>使用 ARM 模板进行资源组部署

本文介绍如何将部署范围限定于资源组。 使用 Azure 资源管理器模板（ARM 模板）进行部署。 本文还介绍了如何在部署操作中将范围扩展到资源组之外。

## <a name="supported-resources"></a>支持的资源

可以将大多数资源部署到资源组。

<!--Not Available on [ARM template reference](https://docs.microsoft.com/azure/templates)-->

## <a name="schema"></a>架构

对于模板，请使用以下架构：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
}
```

对于参数文件，请使用：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>部署命令

若要部署到资源组，请使用资源组部署命令。

[!INCLUDE [azure resource manager update templateurl parameter china](../../../includes/azure-resource-manager-update-templateurl-parameter-china.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

对于 Azure CLI，请使用 [az deployment group create](https://docs.azure.cn/cli/deployment/group#az_deployment_group_create)。 以下示例会部署一个模板来创建资源组：

```azurecli
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

对于 PowerShell 部署命令，请使用 [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment)。 以下示例会部署一个模板来创建资源组：

```powershell
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS `
```

---

有关部署命令和部署 ARM 模板的选项的更多详细信息，请参阅：

* [使用 ARM 模板和 Azure 门户部署资源](deploy-portal.md)
* [使用 ARM 模板和 Azure CLI 部署资源](deploy-cli.md)
* [使用 ARM 模板和 Azure PowerShell 部署资源](deploy-powershell.md)
* [使用 ARM 模板和 Azure 资源管理器 REST API 部署资源](deploy-rest.md)
* [使用部署按钮从 GitHub 存储库部署模板](deploy-to-azure-button.md)

<!--Not Avaialble on [Deploy ARM templates from local Shell](deploy-cloud-shell.md)-->

## <a name="deployment-scopes"></a>部署范围

部署到资源组时，可以将资源部署到：

* 操作中的目标资源组
* 同一订阅或其他订阅中的其他资源组
* 租户中的任何订阅
* 资源组的租户
* [扩展资源](scope-extension-resources.md)可应用于资源

部署模板的用户必须有权访问指定的作用域。

本部分演示如何指定不同范围。 可以在单个模板中组合这些不同范围。

### <a name="scope-to-target-resource-group"></a>目标资源组的范围

若要将资源部署到目标资源，请将这些资源添加到模板的资源部分。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        resource-group-resources
    ],
    "outputs": {}
}
```

有关示例模板，请参阅[部署到目标资源组](#deploy-to-target-resource-group)。

### <a name="scope-to-resource-group-in-same-subscription"></a>将范围限定于同一订阅中的资源组

若要将资源部署到同一订阅中的其他资源组，请添加嵌套部署并包括 `resourceGroup` 属性。 如果未指定订阅 ID 或资源组，将使用父模板中的订阅和资源组。 在运行部署之前，所有资源组都必须存在。

在以下示例中，嵌套部署以名为 `demoResourceGroup` 的资源组为目标。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "nestedDeployment",
            "resourceGroup": "demoResourceGroup",
            "properties": {
                "mode": "Incremental",
                "template": {
                    resource-group-resources
                }
            }
        }
    ],
    "outputs": {}
}
```

有关示例模板，请参阅[部署到多个资源组](#deploy-to-multiple-resource-groups)。

### <a name="scope-to-resource-group-in-different-subscription"></a>将范围限定于不同订阅中的资源组

若要将资源部署到不同订阅中的资源组，请添加嵌套部署并包括 `subscriptionId` 和 `resourceGroup` 属性。 在以下示例中，嵌套部署以名为 `demoResourceGroup` 的资源组为目标。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "nestedDeployment",
            "subscriptionId": "00000000-0000-0000-0000-000000000000",
            "resourceGroup": "demoResourceGroup",
            "properties": {
                "mode": "Incremental",
                "template": {
                    resource-group-resources
                }
            }
        }
    ],
    "outputs": {}
}
```

有关示例模板，请参阅[部署到多个资源组](#deploy-to-multiple-resource-groups)。

### <a name="scope-to-subscription"></a>订阅的范围

若要将资源部署到订阅，请添加嵌套部署并包含 `subscriptionId` 属性。 订阅可以针对目标资源组，也可针对租户中的任何其他订阅。 此外，请为嵌套部署设置 `location` 属性。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "nestedDeployment",
            "location": "chinaeast",
            "subscriptionId": "0000000-0000-0000-0000-000000000000",
            "properties": {
                "mode": "Incremental",
                "template": {
                    subscription-resources
                }
            }
        }
    ],
    "outputs": {}
}
```

有关示例模板，请参阅[创建资源组](#create-resource-group)。

### <a name="scope-to-tenant"></a>将范围设定为租户

可通过将 `scope` 设置为 `/`，在租户中创建资源。 部署模板的用户必须具有[在租户中进行部署所需的访问权限](deploy-to-tenant.md#required-access)。

可使用设置了 `scope` 和 `location` 的嵌套部署。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "nestedDeployment",
            "location": "chinaeast",
            "scope": "/",
            "properties": {
                "mode": "Incremental",
                "template": {
                    tenant-resources
                }
            }
        }
    ],
    "outputs": {}
}
```

或者，可将某些资源类型（如管理组）的范围设置为 `/`。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mgName": {
            "type": "string",
            "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Management/managementGroups",
            "apiVersion": "2020-05-01",
            "name": "[parameters('mgName')]",
            "scope": "/",
            "location": "chinaeast",
            "properties": {}
        }
    ],
    "outputs": {
        "output": {
            "type": "string",
            "value": "[parameters('mgName')]"
        }
    }
}
```

## <a name="deploy-to-target-resource-group"></a>部署到目标资源组

若要在目标资源组部署资源，请在模板的“资源”部分定义这些资源。 以下模板会在部署操作中指定的资源组中创建一个存储帐户。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "type": "string",
      "minLength": 3,
      "maxLength": 11
    },
    "storageSKU": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS",
      ]
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "uniqueStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[variables('uniqueStorageName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "StorageV2",
      "properties": {
        "supportsHttpsTrafficOnly": true
      }
    }
  ],
  "outputs": {
    "storageEndpoint": {
      "type": "object",
      "value": "[reference(variables('uniqueStorageName')).primaryEndpoints]"
    }
  }
}
```

## <a name="deploy-to-multiple-resource-groups"></a>部署到多个资源组

可以在单个 ARM 模板中部署到多个资源组。 若要将与父模板不同的资源组作为目标，请使用[嵌套或链接模板](linked-templates.md)。 在部署资源类型中，为要将嵌套模板部署到的订阅 ID 和资源组指定值。 资源组可以存在于不同的订阅中。

> [!NOTE]
> 在单个部署中可以部署到 800 个资源组。 通常情况下，此限制意味着，在嵌套或链接的部署中可以部署到为父模板指定的一个资源组和最多 799 个资源组。 但是，如果父模板仅包含嵌套或链接的模板，并且本身不部署任何资源，则在嵌套或链接的部署中最多可包含 800 个资源组。

以下示例部署两个存储帐户。 第一个存储帐户部署到在部署操作中指定的资源组。 第二个存储帐户部署到在 `secondResourceGroup` 和 `secondSubscriptionID` 参数中指定的资源组：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    },
    "secondResourceGroup": {
      "type": "string"
    },
    "secondSubscriptionID": {
      "type": "string",
      "defaultValue": ""
    },
    "secondStorageLocation": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "firstStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]",
    "secondStorageName": "[concat(parameters('storagePrefix'), uniqueString(parameters('secondSubscriptionID'), parameters('secondResourceGroup')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('firstStorageName')]",
      "location": "[resourceGroup().location]",
      "sku":{
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "nestedTemplate",
      "resourceGroup": "[parameters('secondResourceGroup')]",
      "subscriptionId": "[parameters('secondSubscriptionID')]",
      "properties": {
      "mode": "Incremental",
      "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[variables('secondStorageName')]",
            "location": "[parameters('secondStorageLocation')]",
            "sku":{
              "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
          }
          ]
      },
      "parameters": {}
      }
    }
  ]
}
```

如果将 `resourceGroup` 设置为不存在的资源组的名称，则部署会失败。

若要测试上述模板并查看结果，请使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要将两个存储帐户部署到 **同一订阅** 中的两个资源组，请使用：

```powershell
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location chinaeast
New-AzResourceGroup -Name $secondRG -Location chinaeast

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation chinaeast
```

若要将两个存储帐户部署到 **两个订阅**，请使用：

```powershell
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Set-AzContext -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location chinaeast

Set-AzContext -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location chinaeast

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation chinaeast `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要将两个存储帐户部署到 **同一订阅** 中的两个资源组，请使用：

```azurecli
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location chinaeast
az group create --name $secondRG --location chinaeast
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=chinaeast
```

若要将两个存储帐户部署到 **两个订阅**，请使用：

```azurecli
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location chinaeast

az account set --subscription $firstSub
az group create --name $firstRG --location chinaeast

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=chinaeast secondSubscriptionID=$secondSub
```

---

## <a name="create-resource-group"></a>创建资源组

可从资源组部署中切换到订阅级别并创建一个资源组。 以下模板会向目标资源组部署一个存储帐户，并在指定的订阅中创建一个新的资源组。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "newResourceGroupName": {
            "type": "string"
        },
        "nestedSubscriptionID": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "demoSubDeployment",
            "location": "chinanorth",
            "subscriptionId": "[parameters('nestedSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Resources/resourceGroups",
                            "apiVersion": "2020-06-01",
                            "name": "[parameters('newResourceGroupName')]",
                            "location": "[parameters('location')]",
                            "properties": {}
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>后续步骤

* 若要通过示例来了解如何为 Azure 安全中心部署工作区设置，请参阅 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)。

<!-- Update_Description: update meta properties, wording update, update link -->