---
title: 使用 Azure 资源管理器模板（ARM 模板）创建 Azure 应用程序配置存储
titleSuffix: Azure App Configuration
description: 了解如何使用 Azure 资源管理器模板（ARM 模板）创建 Azure 应用程序配置存储。
author: ZhijunZhao
ms.author: zhijzhao
ms.date: 10/16/2020
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 02b513c1322e8b232be624725dcacb4d43471dfb
ms.sourcegitcommit: a6aca2f2d1295cd5ed07e38bf9f18f8c345ba409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96190276"
---
# <a name="quickstart-create-an-azure-app-configuration-store-by-using-an-arm-template"></a>快速入门：使用 ARM 模板创建 Azure 应用程序配置存储

本快速入门介绍如何：

- 使用 Azure 资源管理器模板（ARM 模板）部署应用程序配置存储。
- 使用 ARM 模板在应用程序配置存储中创建键值。
- 从 ARM 模板读取应用程序配置存储中的键值。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![“部署到 Azure”](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-app-configuration-store-kv/)。 它创建新的应用程序配置存储，其中包含两个键值。 然后，它使用 `reference` 函数输出这两个键值资源的值。 通过这种方式读取键的值，就能在模板的其他位置使用键值。

本快速入门使用 `copy` 元素来创建多个键值资源实例。 若要详细了解 `copy` 元素，请参阅 [ARM 模板中的资源迭代](../azure-resource-manager/templates/copy-resources.md)。

> [!IMPORTANT]
> 此模板需要的应用程序配置资源提供程序版本为 `2020-07-01-preview` 或更高版本。 此版本使用 `reference` 函数来读取键值。 从 `2020-07-01-preview` 版本开始，将不再提供以前的版本中用于读取键值的 `listKeyValue` 函数。

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "configStoreName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the App Configuration store."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Specifies the Azure location where the app configuration store should be created."
      }
    },
    "keyValueNames": {
      "type": "array",
      "defaultValue": [ "myKey", "myKey$myLabel" ],
      "metadata": {
        "description": "Specifies the names of the key-value resources. The name is a combination of key and label with $ as delimiter. The label is optional."
      }
    },
    "keyValueValues": {
      "type": "array",
      "defaultValue": [ "Key-value without label", "Key-value with label" ],
      "metadata": {
        "description": "Specifies the values of the key-value resources. It's optional"
      }
    },
    "contentType": {
      "type": "string",
      "defaultValue": "the-content-type",
      "metadata": {
        "description": "Specifies the content type of the key-value resources. For feature flag, the value should be application/vnd.microsoft.appconfig.ff+json;charset=utf-8. For Key Value reference, the value should be application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8. Otherwise, it's optional."
      }
    },
    "tags": {
      "type": "object",
      "defaultValue": {
        "tag1": "tag-value-1",
        "tag2": "tag-value-2"
      },
      "metadata": {
        "description": "Adds tags for the key-value resources. It's optional"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.AppConfiguration/configurationStores",
      "apiVersion": "2020-07-01-preview",
      "name": "[parameters('configStoreName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "standard"
      }
    },
    {
      "type": "Microsoft.AppConfiguration/configurationStores/keyValues",
      "apiVersion": "2020-07-01-preview",
      "name": "[concat(parameters('configStoreName'), '/', parameters('keyValueNames')[copyIndex()])]",
      "copy": {
        "name": "keyValueCopy",
        "count": "[length(parameters('keyValueNames'))]"
      },
      "dependsOn": [
        "[parameters('configStoreName')]"
      ],
      "properties": {
        "value": "[parameters('keyValueValues')[copyIndex()]]",
        "contentType": "[parameters('contentType')]",
        "tags": "[parameters('tags')]"
      }
    }
  ],
  "outputs": {
    "reference-key-value-value": {
      "value": "[reference(resourceId('Microsoft.AppConfiguration/configurationStores/keyValues', parameters('configStoreName'), parameters('keyValueNames')[0]), '2020-07-01-preview').value]",
      "type": "string"
    },
    "reference-key-value-object": {
      "value": "[reference(resourceId('Microsoft.AppConfiguration/configurationStores/keyValues', parameters('configStoreName'), parameters('keyValueNames')[1]), '2020-07-01-preview')]",
      "type": "object"
    }
  }
}
```

该模板中定义了两个 Azure 资源：

- [Microsoft.AppConfiguration/configurationStores](https://docs.microsoft.com/azure/templates/microsoft.appconfiguration/2020-06-01/configurationstores)：创建应用程序配置存储。
- Microsoft.AppConfiguration/configurationStores/keyValues：在应用程序配置存储中创建一个键值。

> [!NOTE]
> `keyValues` 资源的名称是键和标签的组合。 键和标签由 `$` 分隔符联接。 标签是可选的。 在上面的示例中，名称为 `myKey` 的 `keyValues` 资源创建了一个不带标签的键值。
>
> 使用百分号编码（也称为 URL 编码），就能在键或标签中包含 ARM 模板资源名称中不允许使用的字符。 `%` 也不是允许使用的字符，因此在其位置使用了 `~`。 若要正确对名称编码，请按照以下步骤操作：
>
> 1. 应用 URL 编码
> 2. 将 `~` 替换为 `~7E`
> 3. 将 `%` 替换为 `~`
>
> 例如，若要创建键名为 `AppName:DbEndpoint` 且标签名为 `Test` 的键值对，资源名应为 `AppName~3ADbEndpoint$Test`。

## <a name="deploy-the-template"></a>部署模板

选择下图登录到 Azure 并打开一个模板。 该模板创建应用程序配置存储，其中包含两个键值。

[![“部署到 Azure”](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

你也可以使用以下 PowerShell cmdlet 部署模板。 键值将位于 PowerShell 控制台的输出中。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-configuration-store-kv/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>查看已部署的资源

1. 登录到 [Azure 门户](https://portal.azure.cn)。
1. 在 Azure 门户搜索框中，键入“应用程序配置”。 从列表中选择“应用程序配置”。
1. 选择新创建的应用程序配置资源。
1. 在“操作”下，单击“配置资源管理器” 。
1. 验证有两个键值。

## <a name="clean-up-resources"></a>清理资源

如果不再需要本教程中所述的资源组、应用程序配置存储和所有相关资源，请将其删除。 如果将来还打算使用该应用程序配置存储，可以跳过删除它的步骤。 如果不打算继续使用该存储，请运行以下 cmdlet，删除本快速入门中创建的所有资源：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>后续步骤

若要了解如何创建使用 Azure 应用程序配置的其他应用程序，请继续阅读以下文章：

> [!div class="nextstepaction"]
> [快速入门：创建使用 Azure 应用程序配置的 ASP.NET Core 应用](quickstart-aspnet-core-app.md)
