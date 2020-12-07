---
title: 将资源部署到租户
description: 介绍如何在 Azure 资源管理器模板中的租户范围内部署资源。
ms.topic: conceptual
origin.date: 10/22/2020
author: rockboyfor
ms.date: 11/30/2020
ms.testscope: yes
ms.testdate: 08/24/2020
ms.author: v-yeche
ms.openlocfilehash: 6c7eb239c505da445bc839b0164866c5f385d9b6
ms.sourcegitcommit: ea52237124974eda84f8cef4bf067ae978d7a87d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024473"
---
<!--Verified successfully-->
# <a name="tenant-deployments-with-arm-templates"></a>使用 ARM 模板进行租户部署

随着组织的成熟，你可能需要在 Azure AD 租户中定义和分配[策略](../../governance/policy/overview.md)或 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md)。 通过租户级模板，可以声明的方式在全局级别应用策略和分配角色。

<!--Mooncake Customization on ## Supported resources-->

## <a name="supported-resources"></a>支持的资源

并非所有资源类型都可以部署到租户级别。 本部分列出了支持的资源类型。

对于 Azure 策略，请使用：

* policyAssignments
* policyDefinitions
* policySetDefinitions

对于 Azure 基于角色的访问控制 (Azure RBAC)，请使用：

* roleAssignments

对于部署到管理组、订阅或资源组的嵌套模板，请使用：

* deployments

对于创建管理组，请使用：

* managementGroups

对于管理成本，请使用：

* billingProfiles
* 说明
* invoiceSections

<!--Mooncake Customization on ## Supported resources-->

## <a name="schema"></a>架构

用于租户部署的架构与资源组部署的架构不同。

对于模板，请使用：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    ...
}
```

对于所有部署范围，参数文件的架构都相同。 对于参数文件，请使用：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="required-access"></a>所需访问权限

部署模板的主体必须具有在租户范围中创建资源的权限。 该主体必须有权执行部署操作 (`Microsoft.Resources/deployments/*`) 和创建模板中定义的资源。 例如，若要创建管理组，主体必须在租户范围内具有参与者权限。 若要创建角色分配，主体则必须具有所有者权限。

Azure Active Directory 的全局管理员不自动拥有分配角色的权限。 若要在租户范围内实现模板部署，全局管理员必须执行以下步骤：

1. 提升帐户访问权限，使其自身可分配角色。 有关详细信息，请参阅[提升访问权限以管理所有 Azure 订阅和管理组](../../role-based-access-control/elevate-access-global-admin.md)。

1. 向需要部署模板的主体分配所有者或参与者角色。

    ```powershell
    New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
    ```

    ```azurecli
    az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
    ```

主体现已具有部署模板所需的权限。

## <a name="deployment-commands"></a>部署命令

用于租户部署的命令与资源组部署使用的命令不同。

[!INCLUDE [azure-resource-manager-update-templateurl-parameter-china](../../../includes/azure-resource-manager-update-templateurl-parameter-china.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

对于 Azure CLI，请使用 [az deployment tenant create](https://docs.azure.cn/cli/deployment/tenant#az_deployment_tenant_create)：

```azurecli
az deployment tenant create \
  --name demoTenantDeployment \
  --location ChinaNorth \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

对于 Azure PowerShell，请使用 [New-AzTenantDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-aztenantdeployment)。

```powershell
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "China North" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

---

有关部署命令和部署 ARM 模板的选项的更多详细信息，请参阅：

* [使用 ARM 模板和 Azure 门户部署资源](deploy-portal.md)
* [使用 ARM 模板和 Azure CLI 部署资源](deploy-cli.md)
* [使用 ARM 模板和 Azure PowerShell 部署资源](deploy-powershell.md)
* [使用 ARM 模板和 Azure 资源管理器 REST API 部署资源](deploy-rest.md)
* [使用部署按钮从 GitHub 存储库部署模板](deploy-to-azure-button.md)

<!--Not Available on * [Deploy ARM templates from local Shell](deploy-cloud-shell.md)-->

## <a name="deployment-scopes"></a>部署范围

部署到管理组时，可以将资源部署到：

* 租户
* 租户中的管理组
* subscriptions
* 资源组（通过两个嵌套部署）
* [扩展资源](scope-extension-resources.md)可应用于资源

部署模板的用户必须有权访问指定的作用域。

本部分演示如何指定不同范围。 可以在单个模板中组合这些不同范围。

### <a name="scope-to-tenant"></a>将范围设定为租户

在模板的资源部分中定义的资源将应用于租户。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        tenant-resources
    ],
    "outputs": {}
}
```

### <a name="scope-to-management-group"></a>将范围设定为管理组

若要以租户内的管理组为目标，请添加嵌套部署并指定 `scope` 属性。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mgName": {
            "type": "string"
        }
    },
    "variables": {
        "mgId": "[concat('Microsoft.Management/managementGroups/', parameters('mgName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "nestedMG",
            "scope": "[variables('mgId')]",
            "location": "chinaeast",
            "properties": {
                "mode": "Incremental",
                "template": {
                    management-group-resources
                }
            }
        }
    ],
    "outputs": {}
}
```

### <a name="scope-to-subscription"></a>订阅的范围

还可以将租户中的订阅作为目标。 部署模板的用户必须有权访问指定的作用域。

若要以租户中的订阅为目标，请使用嵌套部署和 `subscriptionId` 属性。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedSub",
      "location": "chinanorth2",
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              subscription-resources
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="deployment-location-and-name"></a>部署位置和名称

对于租户级别的部署，必须提供部署位置。 部署位置独立于部署的资源的位置。 部署位置指定何处存储部署数据。

可以为部署提供一个名称，也可以使用默认部署名称。 默认名称是模板文件的名称。 例如，部署一个名为 **azuredeploy.json** 的模板将创建默认部署名称 **azuredeploy**。

每个部署名称的位置不可变。 当某个位置中已有某个部署时，无法在另一位置创建同名的部署。 如果出现错误代码 `InvalidDeploymentLocation`，请使用其他名称或使用与该名称的以前部署相同的位置。

## <a name="create-management-group"></a>创建管理组

[以下模板](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/new-mg)用于创建管理组。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
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
      "apiVersion": "2019-11-01",
      "name": "[parameters('mgName')]",
      "properties": {
      }
    }
  ]
}
```

## <a name="assign-role"></a>分配角色

[以下模板](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/tenant-role-assignment)用于在租户范围内分配角色。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "principalId if the user that will be given contributor access to the resourceGroup"
      }
    },
    "roleDefinitionId": {
      "type": "string",
      "defaultValue": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
      "metadata": {
        "description": "roleDefinition for the assignment - default is owner"
      }
    }
  },
  "variables": {
    // This creates an idempotent guid for the role assignment
    "roleAssignmentName": "[guid('/', parameters('principalId'), parameters('roleDefinitionId'))]"
  },
  "resources": [
    {
      "name": "[variables('roleAssignmentName')]",
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2019-04-01-preview",
      "properties": {
        "roleDefinitionId": "[tenantResourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "/"
      }
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

* 若要了解如何分配角色，请参阅[使用 Azure 资源管理器模板添加 Azure 角色分配](../../role-based-access-control/role-assignments-template.md)。
* 还可在[订阅级别](deploy-to-subscription.md)或[管理组级别](deploy-to-management-group.md)部署模板。

<!-- Update_Description: update meta properties, wording update, update link -->