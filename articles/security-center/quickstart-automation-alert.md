---
title: 使用 Azure 资源管理器模板（ARM 模板）为特定安全警报创建安全自动化
description: 了解如何通过使用 Azure 资源管理器模板（ARM 模板）创建 Azure 安全中心自动化来触发逻辑应用，该应用将由特定的安全中心警报触发。
services: azure-resource-manager
author: Johnnytechn
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: v-johya
ms.date: 12/01/2020
ms.openlocfilehash: d9d9f78d68029524f56c6f9a23674dfda37159db
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96432431"
---
# <a name="quickstart-create-an-automatic-response-to-a-specific-security-alert-using-an-arm-template"></a>快速入门：使用 ARM 模板创建特定安全警报自动响应

本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）来创建工作流自动化，以便在 Azure 安全中心收到特定安全警报时触发逻辑应用。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

有关使用Azure 安全中心的工作流自动化功能所需的角色和权限列表，请参阅[工作流自动化](workflow-automation.md)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-securitycenter-create-automation-for-alertnamecontains/)。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "automationName": {
      "type": "string",
      "minLength": 3,
      "maxLength": 24
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for the automation"
      }
    },
    "logicAppName": {
      "type": "string",
      "minLength": 3
    },
    "logicAppResourceGroupName": {
      "type": "string",
      "minLength": 3
    },
    "subscriptionId": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The Azure resource GUID id of the subscription"
      }
    },
    "alertSettings": {
      "type": "object",
      "metadata": {
        "description": "The alert settings object used for deploying the automation"
      }
    }
  },
  "variables": {
    "automationDescription": "automation description for subscription {0}",
    "scopeDescription": "automation scope for subscription {0}"
  },
  "resources": [
    {
      "type": "Microsoft.Security/automations",
      "apiVersion": "2019-01-01-preview",
      "name": "[parameters('automationName')]",
      "location": "[parameters('location')]",
      "properties": {
        "description": "[format(variables('automationDescription'),'{0}', parameters('subscriptionId'))]",
        "isEnabled": true,
        "actions": [
          {
            "actionType": "LogicApp",
            "logicAppResourceId": "[resourceId('Microsoft.Logic/workflows', parameters('logicAppName'))]",
            "uri": "[listCallbackURL(resourceId(parameters('subscriptionId'), parameters('logicAppResourceGroupName'), 'Microsoft.Logic/workflows/triggers', parameters('logicAppName'), 'manual'), '2019-05-01').value]"
          }
        ],
        "scopes": [
          {
            "description": "[format(variables('scopeDescription'),'{0}', parameters('subscriptionId'))]",
            "scopePath": "[subscription().id]"
          }
        ],
        "sources": [
          {
            "eventSource": "Alerts",
            "eventVersionType": "Api",
            "copy": [
              {
                "name": "ruleSets",
                "count": "[length(parameters('alertSettings').alertSeverityMapping)]",
                "input": {
                  "rules": [
                    {
                      "propertyJPath": "[parameters('alertSettings').alertSeverityMapping[copyIndex('ruleSets')].jpath]",
                      "propertyType": "string",
                      "expectedValue": "[parameters('alertSettings').alertSeverityMapping[copyIndex('ruleSets')].expectedValue]",
                      "operator": "[parameters('alertSettings').alertSeverityMapping[copyIndex('ruleSets')].operator]"
                    },
                    {
                      "propertyJPath": "Severity",
                      "propertyType": "string",
                      "expectedValue": "[parameters('alertSettings').alertSeverityMapping[copyIndex('ruleSets')].severity]",
                      "operator": "Equals"
                    }
                  ]
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

### <a name="relevant-resources"></a>相关资源

- [**Microsoft.Security/automations**](https://docs.microsoft.com/azure/templates/microsoft.security/automations)：在收到包含特殊字符串的 Azure 安全中心警报时将触发逻辑应用的自动化。
- [**Microsoft.Logic/workflows**](https://docs.microsoft.com/azure/templates/microsoft.logic/workflows)：可触发的空逻辑应用。

有关其他安全中心快速入门模板，请参阅这些[社区提供的模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Security&pageNumber=1&sort=Popular)。

## <a name="deploy-the-template"></a>部署模板

- PowerShell：

  ```azurepowershell
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
  ```

- **CLI**：

  ```azurecli
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  az group deployment create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
  ```

- **门户**：

  [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

  若要查找有关此部署选项的详细信息，请参阅[使用部署按钮从 GitHub 存储库部署模板](../azure-resource-manager/templates/deploy-to-azure-button.md)。

## <a name="review-deployed-resources"></a>查看已部署的资源

使用 Azure 门户检查是否已部署工作流自动化。

1. 在 [Azure 门户](https://portal.azure.cn)中，打开“安全中心”。
1. 在顶部菜单栏中，选择筛选器图标，然后选择在其上部署了新的工作流自动化的特定订阅。
1. 从安全中心的侧边栏打开“工作流自动化”并查看新的自动化。
    :::image type="content" source="./media/quickstart-automation-alert/validating-template-run.png" alt-text="已配置的自动化列表" lightbox="./media/quickstart-automation-alert/validating-template-run.png":::
    >[!TIP]
    > 如果订阅中有许多工作流自动化，则使用“按名称筛选”选项。

## <a name="clean-up-resources"></a>清理资源

不再需要时，请在 Azure 门户中删除该工作流自动化。

1. 在 [Azure 门户](https://portal.azure.cn)中，打开“安全中心”。
1. 在顶部菜单栏中，选择筛选器图标，然后选择在其上部署了新的工作流自动化的特定订阅。
1. 从安全中心的侧边栏打开“工作流自动化”并查找要删除的自动化。
    :::image type="content" source="./media/quickstart-automation-alert/deleting-workflow-automation.png" alt-text="删除工作流自动化的步骤" lightbox="./media/quickstart-automation-alert/deleting-workflow-automation.png":::
1. 选中要删除的项的复选框。
1. 从工具栏中选择“删除”。

## <a name="next-steps"></a>后续步骤

有关引导你完成模板创建过程的分步教程，请参阅：

> [!div class="nextstepaction"]
> [教程：创建和部署你的第一个 ARM 模板](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

