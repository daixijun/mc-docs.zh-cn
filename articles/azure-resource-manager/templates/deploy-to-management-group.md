---
title: 将资源部署到管理组
description: 介绍如何通过 Azure 资源管理器模板在管理组范围部署资源。
ms.topic: conceptual
origin.date: 11/24/2020
author: rockboyfor
ms.date: 12/14/2020
ms.testscope: yes
ms.testdate: 08/24/2020
ms.author: v-yeche
ms.openlocfilehash: 8c06de11c56b034903b898ff4240bcb5b32ec90e
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97003744"
---
# <a name="management-group-deployments-with-arm-templates"></a>使用 ARM 模板进行管理组部署

随着组织的不断发展，可以部署 Azure 资源管理器模板（ARM 模板）来创建管理组级别的资源。 例如，你可能需要为管理组定义和分配[策略](../../governance/policy/overview.md)或 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md)。 使用管理组级别的模板，可以声明方式在管理组级别应用策略和分配角色。

<!--Mooncake Customization on ## Supported resources-->

## <a name="supported-resources"></a>支持的资源

并非所有资源类型都可以部署到管理组级别。 本部分列出了支持的资源类型。

对于 Azure 蓝图，请使用：

* 项目
* blueprints
* blueprintAssignments
* versions

对于 Azure 策略，请使用：

* policyAssignments
* policyDefinitions
* policySetDefinitions
* remediations

对于 Azure 基于角色的访问控制 (Azure RBAC)，请使用：

* roleAssignments
* roleDefinitions

对于部署到订阅或资源组的嵌套模板，请使用：

* deployments

若要管理资源，请使用：

* tags

<!--Mooncake Customization on ## Supported resources-->

## <a name="schema"></a>架构

用于管理组部署的架构不同于资源组部署的架构。

对于模板，请使用：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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

## <a name="deployment-commands"></a>部署命令

若要部署到管理组，请使用管理组部署命令。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

对于 Azure CLI，请使用 [az deployment mg create](https://docs.azure.cn/cli/deployment/mg#az_deployment_mg_create)：

```azurecli
az deployment mg create \
  --name demoMGDeployment \
  --location chinanorth \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

对于 Azure PowerShell，请使用 [New-AzManagementGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azmanagementgroupdeployment)。

```powershell
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "China North" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

---

有关部署命令和部署 ARM 模板的选项的更多详细信息，请参阅：

* [使用 ARM 模板和 Azure 门户部署资源](deploy-portal.md)
* [使用 ARM 模板和 Azure CLI 部署资源](deploy-cli.md)
* [使用 ARM 模板和 Azure PowerShell 部署资源](deploy-powershell.md)
* [使用 ARM 模板和 Azure 资源管理器 REST API 部署资源](deploy-rest.md)
* [使用部署按钮从 GitHub 存储库部署模板](deploy-to-azure-button.md)
    
    <!--Not Available on * [Deploy ARM templates from local Shell](deploy-cloud-shell.md)-->

## <a name="deployment-location-and-name"></a>部署位置和名称

对于管理组级别部署，必须为部署提供位置。 部署位置独立于部署的资源的位置。 部署位置指定何处存储部署数据。 [订阅](deploy-to-subscription.md)和[租户](deploy-to-tenant.md)部署也需要位置。 对于[资源组](deploy-to-resource-group.md)部署，资源组的位置用于存储部署数据。

可以为部署提供一个名称，也可以使用默认部署名称。 默认名称是模板文件的名称。 例如，部署一个名为 **azuredeploy.json** 的模板将创建默认部署名称 **azuredeploy**。

每个部署名称的位置不可变。 当某个位置中已有某个部署时，无法在另一位置创建同名的部署。 例如，如果在 chinaeast 中创建名为“deployment1”的管理组部署，则以后不能创建另一个名为“deployment1”但位置为“chinanorth”的部署。 如果出现错误代码 `InvalidDeploymentLocation`，请使用其他名称或使用与该名称的以前部署相同的位置。

## <a name="deployment-scopes"></a>部署范围

部署到管理组时，可以将资源部署到：

* 操作的目标管理组
* 租户中的另一个管理组
* 管理组中的订阅
* 管理组中的资源组
* 资源组的租户
* [扩展资源](scope-extension-resources.md)可应用于资源

部署模板的用户必须有权访问指定的作用域。

本部分演示如何指定不同范围。 可以在单个模板中组合这些不同范围。

### <a name="scope-to-target-management-group"></a>将范围设定为目标管理组

将通过部署命令对管理组应用模板的资源部分中定义的资源。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        management-group-resources-default
    ],
    "outputs": {}
}
```

### <a name="scope-to-another-management-group"></a>将范围设定为另一个管理组

若要以另一个管理组为目标，请添加嵌套部署并指定 `scope` 属性。 将 `scope` 属性设置为 `Microsoft.Management/managementGroups/<mg-name>` 格式的值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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
            "apiVersion": "2019-10-01",
            "name": "nestedDeployment",
            "scope": "[variables('mgId')]",
            "location": "chinaeast",
            "properties": {
                "mode": "Incremental",
                "template": {
                    management-group-resources-non-default
                }
            }
        }
    ],
    "outputs": {}
}
```

### <a name="scope-to-subscription"></a>订阅的范围

还可以将管理组中的订阅作为目标。 部署模板的用户必须有权访问指定的作用域。

若要以管理组中的订阅为目标，请使用嵌套部署和 `subscriptionId` 属性。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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

### <a name="scope-to-resource-group"></a>将范围限定于资源组

还可以将管理组中的资源组作为目标。 部署模板的用户必须有权访问指定的作用域。

若要以管理组中的资源组为目标，请使用嵌套部署。 设置 `subscriptionId` 和 `resourceGroup` 属性。 不要为嵌套部署设置位置，因为它部署在资源组的位置。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedRGDeploy",
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "resourceGroup": "demoResourceGroup",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              resource-group-resources
            }
          ]
        }
      }
    }
  ]
}
```

若要使用管理组部署在订阅中创建资源组并将存储帐户部署到该资源组，请参阅[部署到订阅和资源组](#deploy-to-subscription-and-resource-group)。

### <a name="scope-to-tenant"></a>将范围设定为租户

可以通过将 `scope` 设置为 `/`，在租户中创建资源。 部署模板的用户必须具有[在租户中进行部署所需的访问权限](deploy-to-tenant.md#required-access)。

可以使用设置了 `scope` 和 `location` 的嵌套部署。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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

或者，可以将某些资源类型（如管理组）的 scope 设置为 `/`。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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

## <a name="azure-policy"></a>Azure Policy

部署到管理组的自定义策略定义是管理组的扩展。 若要获取自定义策略定义的 ID，请使用 [extensionResourceId()](template-functions-resource.md#extensionresourceid) 函数。 内置策略定义是租户级别资源。 若要获取内置策略定义的 ID，请使用 [tenantResourceId](template-functions-resource.md#tenantresourceid) 函数。

下面的示例演示如何[定义](../../governance/policy/concepts/definition-structure.md)管理组级别策略，并对其进行分配。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMG": {
            "type": "string",
            "metadata": {
                "description": "Target Management Group"
            }
        },
        "allowedLocations": {
            "type": "array",
            "defaultValue": [
                "chinaeast2",
                "chinaeast",
                "chinanorth"
            ],
            "metadata": {
                "description": "An array of the allowed locations, all other locations will be denied by the created policy."
            }
        }
    },
    "variables": {
        "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
        "policyDefinition": "LocationRestriction"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "[variables('policyDefinition')]",
            "apiVersion": "2019-09-01",
            "properties": {
                "policyType": "Custom",
                "mode": "All",
                "parameters": {
                },
                "policyRule": {
                    "if": {
                        "not": {
                            "field": "location",
                            "in": "[parameters('allowedLocations')]"
                        }
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2019-09-01",
            "dependsOn": [
                "[variables('policyDefinition')]"
            ],
            "properties": {
                "scope": "[variables('mgScope')]",
                "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
            }
        }
    ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>部署到订阅和资源组

在管理组级别的部署中，可以以管理组中的订阅为目标。 以下示例在一个订阅中创建资源组，并向该资源组部署存储帐户。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "nestedsubId": {
            "type": "string"
        },
        "nestedRG": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        },
        "nestedLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "nestedSub",
            "location": "[parameters('nestedLocation')]",
            "subscriptionId": "[parameters('nestedSubId')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                    },
                    "variables": {
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Resources/resourceGroups",
                            "apiVersion": "2020-06-01",
                            "name": "[parameters('nestedRG')]",
                            "location": "[parameters('nestedLocation')]"
                        }
                    ]
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "nestedRG",
            "subscriptionId": "[parameters('nestedSubId')]",
            "resourceGroup": "[parameters('nestedRG')]",
            "dependsOn": [
                "nestedSub"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2019-04-01",
                            "name": "[parameters('storageAccountName')]",
                            "location": "[parameters('nestedLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>后续步骤

* 若要了解如何分配角色，请参阅[使用 Azure 资源管理器模板添加 Azure 角色分配](../../role-based-access-control/role-assignments-template.md)。
* 若要通过示例来了解如何为 Azure 安全中心部署工作区设置，请参阅 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)。
* 还可以在[订阅级别](deploy-to-subscription.md)和[租户级别](deploy-to-tenant.md)部署模板。

<!-- Update_Description: update meta properties, wording update, update link -->