---
title: Azure 快速入门 - 使用 Azure 资源管理器模板创建 Azure Key Vault 和机密 | Microsoft Docs
description: 介绍如何使用 Azure 资源管理器模板创建 Azure Key Vault，并将机密添加到保管库的快速入门。
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc,subject-armqs
origin.date: 02/27/2020
ms.date: 11/27/2020
ms.author: v-tawe
ms.openlocfilehash: 2570467142fc5550c684c283d407cf2f386e84fa
ms.sourcegitcommit: 87b6bb293f39c5cfc2db6f38547220a13816d78f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96431196"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-an-arm-template"></a>快速入门：使用 ARM 模板在 Azure Key Vault 中设置和检索机密

[Azure Key Vault](../general/overview.md) 是为密钥、密码、证书等机密及其他机密提供安全存储的云服务。 本快速入门重点介绍部署 Azure 资源管理器模板（ARM 模板）以创建密钥保管库和机密的过程。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

若要完成本文，需要做好以下准备：

* 如果没有 Azure 订阅，请在开始前创建一个[试用订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

* 模板需要使用你的 Azure AD 用户对象 ID 来配置权限。 以下过程获取对象 ID (GUID)。

    1. 运行以下 Azure PowerShell 或 Azure CLI 命令。

        # <a name="cli"></a>[CLI](#tab/CLI)
        ```azurecli
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    2. 请记下对象 ID， 本快速入门的下一部分需要使用该 ID。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-key-vault-create/)。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Specifies the Azure location where the key vault should be created."
      }
    },
    "enabledForDeployment": {
      "type": "bool",
      "defaultValue": false,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies whether Azure Virtual Machines are permitted to retrieve certificates stored as secrets from the key vault."
      }
    },
    "enabledForDiskEncryption": {
      "type": "bool",
      "defaultValue": false,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies whether Azure Disk Encryption is permitted to retrieve secrets from the vault and unwrap keys."
      }
    },
    "enabledForTemplateDeployment": {
      "type": "bool",
      "defaultValue": false,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies whether Azure Resource Manager is permitted to retrieve secrets from the key vault."
      }
    },
    "tenantId": {
      "type": "string",
      "defaultValue": "[subscription().tenantId]",
      "metadata": {
        "description": "Specifies the Azure Active Directory tenant ID that should be used for authenticating requests to the key vault. Get it by using Get-AzSubscription cmdlet."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the secret that you want to create."
      }
    },
    "secretValue": {
      "type": "securestring",
      "metadata": {
        "description": "Specifies the value of the secret that you want to create."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "[parameters('keyVaultName')]",
      "apiVersion": "2018-02-14",
      "location": "[parameters('location')]",
      "properties": {
        "enabledForDeployment": "[parameters('enabledForDeployment')]",
        "enabledForDiskEncryption": "[parameters('enabledForDiskEncryption')]",
        "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
        "tenantId": "[parameters('tenantId')]",
        "accessPolicies": [
          {
            "objectId": "[parameters('objectId')]",
            "tenantId": "[parameters('tenantId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]"      
            }
          }
        ],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "value": {
            "defaultAction": "Allow",
            "bypass": "AzureServices"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/secrets",
      "name": "[concat(parameters('keyVaultName'), '/', parameters('secretName'))]",
      "apiVersion": "2018-02-14",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "properties": {
        "value": "[parameters('secretValue')]"
      }
    }
  ]
}
```

该模板中定义了两个 Azure 资源：

* [**Microsoft.KeyVault/vaults**](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults)：创建 Azure 密钥保管库。
* [**Microsoft.KeyVault/vaults/secrets**](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/secrets)：创建密钥保管库机密。

可以在 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular)中找到更多 Azure Key Vault 模板示例。

## <a name="deploy-the-template"></a>部署模板

1. 选择下图登录到 Azure 并打开一个模板。 该模板将创建 Key Vault 和机密。

    [![部署到 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

2. 选择或输入以下值。

    ![ARM 模板, Key Vault 集成, 部署门户](../media/quick-create-template/create-key-vault-using-template-portal.png)

    除非另有指定，否则请使用默认值创建密钥保管库和机密。

    * 订阅：选择一个 Azure 订阅。
    * 资源组：选择“新建”，输入资源组的唯一名称，然后单击“确定”。 
    * 位置：选择一个位置。  例如，“中国东部 2”。
    * **密钥保管库名称**：输入密钥保管库的名称，该名称在 .vault.azure.cn 命名空间中必须全局唯一。 在下一部分验证部署时，需要该名称。
    * **租户 ID**：模板函数会自动检索租户 ID。 不要更改默认值。
    * **AD 用户 ID**：输入在 [先决条件](#prerequisites)中检索到的 Azure AD 用户对象 ID。
    * **机密名称**：输入要存储在 Key Vault 中的机密的名称。 例如 **adminpassword**。
    * **机密值**：输入机密值。 如果存储密码，则建议使用在“先决条件”中创建的生成密码。
    * **我同意上述条款和条件**：选中。
3. 选择“购买”。 成功部署密钥保管库后，你会收到通知：

    ![ARM 模板, Key Vault 集成, 部署门户通知](../media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

使用 Azure 门户部署模板。 除了 Azure 门户，还可以使用 Azure PowerShell、Azure CLI 和 REST API。 若要了解其他部署方法，请参阅[部署模板](../../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="review-deployed-resources"></a>查看已部署的资源

可以使用 Azure 门户检查 Key Vault 和机密，或者使用以下 Azure CLI 或 Azure PowerShell 脚本列出创建的机密。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

输出如下所示：

# <a name="cli"></a>[CLI](#tab/CLI)

![屏幕截图显示了 CLI 中的部署门户验证输出。](../media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

![ARM 模板, Key Vault 集成, 部署门户验证输出](../media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---

## <a name="clean-up-resources"></a>清理资源

其他 Key Vault 快速入门和教程是在本快速入门的基础上制作的。 如果打算继续使用后续的快速入门和教程，则可能需要保留这些资源。
如果不再需要资源组，可以将其删除，这将删除 Key Vault 和相关的资源。 使用 Azure CLI 或 Azure PowerShell 删除资源组：

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>后续步骤

在本快速入门中，你使用 ARM 模板创建了密钥保管库和机密，并验证了部署。 若要详细了解 Key Vault 和 Azure 资源管理器，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 了解有关 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)的详细信息
- 查看 [Azure Key Vault 最佳做法](../general/best-practices.md)
