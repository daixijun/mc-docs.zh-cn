---
title: 快速入门：创建包含使用者组的事件中心 - Azure 事件中心
description: 快速入门：使用 Azure 资源管理器模板创建包含事件中心和使用者组的事件中心命名空间
ms.topic: quickstart
ms.custom: subject-armqs
origin.date: 06/23/2020
ms.date: 09/14/2020
ms.author: v-tawe
ms.openlocfilehash: 58c741ee6dba3e3d04843c9f72c6cd58b62c0ecb
ms.sourcegitcommit: 87b6bb293f39c5cfc2db6f38547220a13816d78f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96430849"
---
# <a name="quickstart-create-an-event-hub-by-using-an-arm-template"></a>快速入门：使用 ARM 模板创建事件中心

Azure 事件中心是一个大数据流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。 事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到数据中心的数据。 有关事件中心的详细概述，请参阅[事件中心概述](event-hubs-about.md)和[事件中心功能](event-hubs-features.md)。 本快速入门将使用 [Azure 资源管理器模板（ARM 模板）](../azure-resource-manager/management/overview.md)创建事件中心。 然后部署 ARM 模板，以创建包含一个事件中心的[事件中心](./event-hubs-about.md)类型的命名空间。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-eventhubs-create-namespace-and-eventhub%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始前创建一个[试用版订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-eventhubs-create-namespace-and-eventhub/)。

{ "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", "contentVersion":"1.0.0.0", "parameters": { "projectName":{ "type": "string", "metadata": { "description":"指定用于生成事件中心名称和命名空间名称的项目名称。"
} }, "location": { "type": "string", "defaultValue": "[resourceGroup().location]", "metadata": { "description":"指定所有资源的 Azure 位置。"
} }, "eventHubSku": { "type": "string", "allowedValues": [ "Basic", "Standard" ], "defaultValue":"Standard", "metadata": { "description":"指定服务总线命名空间的消息传递层。"
} } }, "variables": { "eventHubNamespaceName": "[concat(parameters('projectName'), 'ns')]", "eventHubName": "[parameters('projectName')]" }, "resources": [ { "apiVersion":"2017-04-01", "type":"Microsoft.EventHub/namespaces", "name": "[variables('eventHubNamespaceName')]", "location": "[parameters('location')]", "sku": { "name": "[parameters('eventHubSku')]", "tier": "[parameters('eventHubSku')]", "capacity":1 }, "properties": { "isAutoInflateEnabled": false, "maximumThroughputUnits":0 } }, { "apiVersion":"2017-04-01", "type":"Microsoft.EventHub/namespaces/eventhubs", "name": "[concat(variables('eventHubNamespaceName'), '/', variables('eventHubName'))]", "location": "[parameters('location')]", "dependsOn": [ "[resourceId('Microsoft.EventHub/namespaces', variables('eventHubNamespaceName'))]" ], "properties": { "messageRetentionInDays":7, "partitionCount":1 } } ] }
```

The resources defined in the template include:

- [**Microsoft.EventHub/namespaces**](https://docs.microsoft.com/azure/templates/microsoft.eventhub/namespaces)
- [**Microsoft.EventHub/namespaces/eventhubs**](https://docs.microsoft.com/azure/templates/microsoft.eventhub/namespaces/eventhubs)

To find more template samples, see [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/?term=eventhub&pageNumber=1&sort=Popular).

## Deploy the template

To deploy the template:

1. Sign in to the Azure PowerShell.

   ```azurepowershell
   $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. chinaeast)"
   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName

   Write-Host "Press [ENTER] to continue ..."
   ```

   创建事件中心需要一些时间。

1. 选择“复制”以复制 PowerShell 脚本。
1. 右键单击 shell 控制台并选择“粘贴”。

## <a name="validate-the-deployment"></a>验证部署

若要验证部署，可以从 [Azure 门户](https://portal.azure.cn)打开资源组，或使用以下 Azure PowerShell 脚本。

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"
$namespaceName = "${projectName}ns"

Get-AzEventHub -ResourceGroupName $resourceGroupName -Namespace $namespaceName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>后续步骤

在本文中，你已创建一个事件中心命名空间，并在该命名空间中创建了一个事件中心。 有关如何将事件发送到事件中心（或）从事件中心接收事件的分步说明，请参阅“发送和接收事件”教程：

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C（仅发送）](event-hubs-c-getstarted-send.md)
- [Apache Storm（仅接收）](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
