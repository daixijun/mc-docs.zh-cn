---
title: 使用模板创建 Azure 服务总线命名空间
description: 使用 Azure 资源管理器模板创建服务总线消息命名空间
documentationcenter: .net
author: rockboyfor
ms.topic: article
ms.tgt_pltfrm: dotnet
origin.date: 06/23/2020
ms.date: 08/24/2020
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.openlocfilehash: 94b46c4b30214f32925334acf2b4ee2b0975d976
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96431837"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板创建服务总线命名空间

了解如何部署 Azure 资源管理器模板以创建服务总线命名空间。 可将此模板用于自己的部署，或自定义此模板以满足要求。 有关创建模板的详细信息，请参阅 [Azure 资源管理器文档](../azure-resource-manager/index.yml)。

还可使用以下模板创建服务总线命名空间：

* [创建包含队列的服务总线命名空间](./service-bus-resource-manager-namespace-queue.md)
* [创建包含主题和订阅的服务总线命名空间](./service-bus-resource-manager-namespace-topic.md)
* [创建包含队列和授权规则的服务总线命名空间](./service-bus-resource-manager-namespace-auth-rule.md)
* [创建包含主题、订阅和规则的服务总线命名空间](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果没有 Azure 订阅，请在开始前[创建一个试用版订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="create-a-service-bus-namespace"></a>创建服务总线命名空间

在本快速入门中，使用 [Azure 快速启动模板](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json)中的[现有资源管理器模板](https://github.com/Azure/azure-quickstart-templates/)：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusSku": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Standard",
      "metadata": {
        "description": "The messaging tier for service Bus namespace"
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
    "defaultSASKeyName": "RootManageSharedAccessKey",
    "defaultAuthRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]",
    "sbVersion": "2017-04-01"
  },
  "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('serviceBusNamespaceName')]",
      "type": "Microsoft.ServiceBus/namespaces",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('serviceBusSku')]"
      }
    }
  ],
  "outputs": {
    "NamespaceDefaultConnectionString": {
      "type": "string",
      "value": "[listkeys(variables('defaultAuthRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
    },
    "DefaultSharedAccessPolicyPrimaryKey": {
      "type": "string",
      "value": "[listkeys(variables('defaultAuthRuleResourceId'), variables('sbVersion')).primaryKey]"
    }
  }
}
```

若要查找更多模板示例，请参阅 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)。

若要通过部署模板创建服务总线命名空间，请执行以下操作：

1. 在本地计算机上使用 PowerShell 登录到 Azure 中国。

    ```powershell
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. chinaeast)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    资源组名称是追加了 **rg** 的服务总线命名空间名称。

2. 选择“复制”以复制 PowerShell 脚本。
3. 右键单击 shell 控制台并选择“粘贴”  。

创建事件中心需要花费片刻时间。

## <a name="verify-the-deployment"></a>验证部署

若要查看部署的服务总线命名空间，可以从 Azure 门户打开资源组，或者使用以下 Azure PowerShell 脚本。 如果 PowerShell 仍处于打开状态，则无需复制/运行以下脚本的第一行和第二行。

```powershell
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

在本教程中，请使用 Azure PowerShell 来部署模板。 如需其他模板部署方法，请参阅：

* [使用 Azure 门户](../azure-resource-manager/templates/deploy-portal.md)。
* [使用 Azure CLI](../azure-resource-manager/templates/deploy-cli.md)。
* [使用 REST API](../azure-resource-manager/templates/deploy-rest.md)。

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。 如果 PowerShell 仍处于打开状态，则无需复制/运行以下脚本的第一行和第二行。

```powershell
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>后续步骤

在本文中，我们已创建一个服务总线命名空间。 请参阅其他快速入门，了解如何创建和使用队列、主题/订阅：

* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [服务总线主题入门](service-bus-dotnet-how-to-use-topics-subscriptions.md)

<!-- Update_Description: update meta properties, wording update, update link -->