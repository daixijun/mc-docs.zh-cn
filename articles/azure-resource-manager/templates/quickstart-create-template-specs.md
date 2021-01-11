---
title: 创建和部署模板规格
description: 了解如何通过 ARM 模板创建模板规格。 然后，将模板规格部署到订阅中的资源组。
origin.date: 12/14/2020
author: rockboyfor
ms.date: 01/11/2021
ms.testscope: no
ms.testdate: ''
ms.topic: quickstart
ms.author: v-yeche
ms.openlocfilehash: 5c37e66c6b53efdb2ba2f7aa0063c4e15e61aacf
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98022920"
---
<!--Verified successfully-->
<!--REASON: IS NOT PRIVATE PREVIEW TILL ON 01/06/2021-->
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>快速入门：创建和部署模板规格（预览）

本快速入门介绍如何将 Azure 资源管理器模板（ARM 模板）打包为[模板规格](template-specs.md)。然后部署该模板规格。模板规格包含用于部署存储帐户的 ARM 模板。

## <a name="prerequisites"></a>先决条件

具有活动订阅的 Azure 帐户。 [创建试用版订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

> [!NOTE]
> 模板规格当前提供预览版。 若要将其与 Azure PowerShell 一起使用，必须安装[版本 5.0.0 或更高版本](https://docs.microsoft.com/powershell/azure/install-az-ps)。 若要将其与 Azure CLI 一起使用，请使用[版本 2.14.2 或更高版本](https://docs.azure.cn/cli/install-azure-cli)。

## <a name="create-template"></a>创建模板

可以根据本地模板创建模板规格。 复制以下模板并将其本地保存到名为 azuredeploy.json 的文件中。 本快速入门假设你已将模板保存到路径 c:\Templates\azuredeploy.json，不过你可以使用任何路径。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
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
    
    "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountEndPoint": "https://core.chinacloudapi.cn/",
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

## <a name="create-template-spec"></a>创建模板规格

模板规格是名为 `Microsoft.Resources/templateSpecs` 的资源类型。 若要创建模板规格，可以使用 PowerShell、Azure CLI、门户或 ARM 模板。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 创建新的资源组以包含模板规格。

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location chinanorth2
    ```

1. 在该资源组中创建模板规格。 将新的模板规格命名为 storageSpec。

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location chinanorth2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. 创建新的资源组以包含模板规格。

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location chinanorth2
    ```

1. 在该资源组中创建模板规格。 将新的模板规格命名为 storageSpec。

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "chinanorth2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

<!--Not Available till on 01/06/2021 # [Portal](#tab/azure-portal)-->

# <a name="arm-template"></a>[ARM 模板](#tab/azure-resource-manager)

> [!NOTE]
> 建议不使用 ARM 模板，而是使用 PowerShell 或 CLI 来创建模板规格。这些工具会自动将链接的模板转换为连接到主模板的项目。 使用 ARM 模板创建模板规格时，必须手动将这些链接的模板添加为项目，这可能会很复杂。

1. 使用 ARM 模板创建模板规格时，该模板将嵌入资源定义。 你需对本地模板进行一些更改。 复制以下模板并将其本地保存为 azuredeploy.json。

    > [!NOTE]
    > 在嵌入的模板中，必须使用第二个左括号对所有[模板表达式](template-expressions.md)进行转义。 请使用 `"[[`，而不是 `"[`。 JSON 数组仍使用单个左括号。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "chinanorth2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "1.0",
              "location": "chinanorth2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "parameters": {
                    "storageAccountType": {
                      "type": "string",
                      "defaultValue": "Standard_LRS",
                      "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Premium_LRS"
                      ],
                      "metadata": {
                        "description": "Storage Account type"
                      }
                    },
                    "location": {
                      "type": "string",
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    }
                  },
                  "variables": {
                    "storageAccountEndPoint": "https://core.chinacloudapi.cn/",
                    "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountEndPoint": "https://core.chinacloudapi.cn/",
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. 使用 Azure CLI 或 PowerShell 创建新的资源组。

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location chinanorth2
    ```

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location chinanorth2
    ```

1. 使用 Azure CLI 或 PowerShell 部署模板。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

---

## <a name="deploy-template-spec"></a>部署模板规格

若要部署模板规格，请使用部署模板所用的部署命令。 传入模板规格的资源 ID 以进行部署。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 创建资源组以包含新的存储帐户。

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location chinanorth2
    ```

1. 获取模板规格的资源 ID。

    ```azurepowershell
    $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Versions.Id
    ```

1. 部署模板规格。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG
    ```

1. 提供的参数与 ARM 模板的完全一样。 使用存储帐户类型的参数重新部署模板规格。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG `
      -storageAccountType Standard_GRS
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. 创建资源组以包含新的存储帐户。

    ```azurecli
    az group create \
      --name storageRG \
      --location chinanorth2
    ```

1. 获取模板规格的资源 ID。

    ```azurecli
    id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
    ```

    > [!NOTE]
    > 获取模板规格 ID 并将其分配到 Windows PowerShell 中的变量时存在一个已知问题。

1. 部署模板规格。

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id
    ```

1. 提供的参数与 ARM 模板的完全一样。 使用存储帐户类型的参数重新部署模板规格。

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters storageAccountType='Standard_GRS'
    ```

<!--Not Available till on 01/06/2021 # [Portal](#tab/azure-portal)-->


# <a name="arm-template"></a>[ARM 模板](#tab/azure-resource-manager)

1. 复制以下模板并将其本地保存到名为 storage.json 的文件中。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. 使用 Azure CLI 或 PowerShell 为存储帐户创建新的资源组。

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location chinanorth2
    ```

    ```azurecli
    az group create \
      --name storageRG \
      --location chinanorth2
    ```

1. 使用 Azure CLI 或 PowerShell 部署模板。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="grant-access"></a>授予访问权限

如果要让组织中的其他用户部署模板规格，你需要向他们授予读取权限。 对于包含要共享的模板规格的资源组，你可以将读取者角色分配给相应的 Azure AD 组。 有关详细信息，请参阅[教程：使用 Azure PowerShell 授予组对 Azure 资源的访问权限](../../role-based-access-control/tutorial-role-assignments-group-powershell.md)。

## <a name="update-template"></a>更新模板

假设你已在模板规格中标识了要对模板进行的更改。以下模板类似于之前的模板，不同之处在于其存储帐户名称增加了前缀。 复制以下模板并更新 azuredeploy.json 文件。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    },
    "namePrefix": {
        "type": "string",
        "maxLength": 11,
        "defaultValue": "store",
        "metadata": {
            "description": "Prefix for storage account name"
        }
    }
  },
  "variables": {
    "storageAccountEndPoint": "https://core.chinacloudapi.cn/",
    "storageAccountName": "[concat(parameters('namePrefix'), uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountEndPoint": "https://core.chinacloudapi.cn/",
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

## <a name="update-template-spec-version"></a>更新模板规格版本

在现有模板规格中添加名为 `2.0` 的新版本，而不是为修改后的模板创建新的模板规格。用户可以选择任一版本进行部署。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 创建新的模板规格版本。

    ```powershell
    New-AzTemplateSpec `
     -Name storageSpec `
     -Version "2.0" `
     -ResourceGroupName templateSpecRG `
     -Location chinanorth2 `
     -TemplateFile "c:\Templates\azuredeploy.json"
    ```

1. 若要部署新版本，请获取 `2.0` 版本的资源 ID。

    ```powershell
    $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "2.0").Versions.Id
    ```

1. 部署该版本。 为存储帐户名称提供一个前缀。

    ```powershell
    New-AzResourceGroupDeployment `
     -TemplateSpecId $id `
     -ResourceGroupName storageRG `
     -namePrefix "demoaccount"
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. 创建新的模板规格版本。

    ```azurecli
    az ts create \
     --name storageSpec \
     --version "2.0" \
     --resource-group templateSpecRG \
     --location "chinanorth2" \
     --template-file "c:\Templates\azuredeploy.json"
    ```

1. 若要部署新版本，请获取 `2.0` 版本的资源 ID。

    ```azurecli
    id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "2.0" --query "id")
    ```

1. 部署该版本。 为存储帐户名称提供一个前缀。

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters namePrefix='demoaccount'
    ```

<!--Not Available till on 01/06/2021 # [Portal](#tab/azure-portal)-->

# <a name="arm-template"></a>[ARM 模板](#tab/azure-resource-manager)

1. 同样，必须对本地模板进行一些更改，确保其在模板规格下正常工作。 复制以下模板并将其本地保存为 azuredeploy.json。

   ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "chinanorth2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "2.0",
              "location": "chinanorth2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "parameters": {
                    "storageAccountType": {
                      "type": "string",
                      "defaultValue": "Standard_LRS",
                      "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Premium_LRS"
                      ],
                      "metadata": {
                        "description": "Storage Account type"
                      }
                    },
                    "location": {
                      "type": "string",
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    },
                    "namePrefix": {
                      "type": "string",
                      "maxLength": 11,
                      "defaultValue": "store",
                      "metadata": {
                        "description": "Prefix for storage account name"
                      }
                    }
                  },
                  "variables": {
                    "storageAccountEndPoint": "https://core.chinacloudapi.cn/",
                    "storageAccountName": "[[concat(parameters('namePrefix'), uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountEndPoint": "https://core.chinacloudapi.cn/",
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. 若要将新版本添加到模板规格中，请使用 Azure CLI 或 PowerShell 部署模板。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

1. 复制以下模板并将其本地保存到名为 storage.json 的文件中。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '2.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. 使用 Azure CLI 或 PowerShell 部署模板。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="clean-up-resources"></a>清理资源

若要清理本快速入门中部署的资源，请删除创建的两个资源组。

1. 在 Azure 门户上的左侧菜单中选择“资源组”。

1. 在“按名称筛选”字段中输入资源组名称（templateSpecRG 和 storageRG）。

1. 选择资源组名称。

1. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

若要了解有关创建包含关联模板的模板规格的信息，请参阅[创建关联模板的模板规格](template-specs-create-linked.md)。

<!-- Update_Description: update meta properties, wording update, update link -->