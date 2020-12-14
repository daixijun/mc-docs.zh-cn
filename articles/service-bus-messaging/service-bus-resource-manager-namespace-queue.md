---
title: 使用 Azure 模板创建 Azure 服务总线命名空间和队列
description: 快速入门 - 使用 Azure 资源管理器模板创建服务总线命名空间和队列
documentationcenter: .net
author: rockboyfor
ms.topic: quickstart
ms.custom: subject-armqs
ms.tgt_pltfrm: dotnet
origin.date: 06/23/2020
ms.date: 08/24/2020
ms.testscope: no
ms.testdate: 08/17/2020
ms.author: v-yeche
ms.openlocfilehash: 8bff2454133562ad4396878fed4c7402c5be7a97
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96431841"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-arm-template"></a>快速入门：使用 ARM 模板创建服务总线命名空间和队列

本文介绍如何使用创建服务总线命名空间和命名空间中队列的 Azure 资源管理器模板（ARM 模板）。 本文介绍如何指定要部署的资源以及如何定义执行部署时指定的参数。 可将此模板用于自己的部署，或自定义此模板以满足要求。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署到 Azure":::](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始前[创建一个试用版订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue)。

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
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
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
    "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]",
    "sbVersion": "2017-04-01"
  },
  "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('serviceBusNamespaceName')]",
      "type": "Microsoft.ServiceBus/namespaces",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "type": "Queues",
          "dependsOn": [
            "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "lockDuration": "PT5M",
            "maxSizeInMegabytes": "1024",
            "requiresDuplicateDetection": "false",
            "requiresSession": "false",
            "defaultMessageTimeToLive": "P10675199DT2H48M5.4775807S",
            "deadLetteringOnMessageExpiration": "false",
            "duplicateDetectionHistoryTimeWindow": "PT10M",
            "maxDeliveryCount": "10",
            "autoDeleteOnIdle": "P10675199DT2H48M5.4775807S",
            "enablePartitioning": "false",
            "enableExpress": "false"
          }
        }
      ]
    }
  ],
  "outputs": {
    "NamespaceConnectionString": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
    },
    "SharedAccessPolicyPrimaryKey": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
    }
  }
}
```

该模板中定义的资源包括：

- [**Microsoft.ServiceBus/namespaces**](https://docs.microsoft.com/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft.ServiceBus/namespaces/queues**](https://docs.microsoft.com/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> 以下 ARM 模板可供下载和部署。
>
> * [创建包含队列和授权规则的服务总线命名空间](service-bus-resource-manager-namespace-auth-rule.md)
> * [创建包含主题和订阅的服务总线命名空间](service-bus-resource-manager-namespace-topic.md)
> * [创建服务总线命名空间](service-bus-resource-manager-namespace.md)
> * [创建包含主题、订阅和规则的服务总线命名空间](service-bus-resource-manager-namespace-topic-with-rule.md)

可以从 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)中找到更多模板

## <a name="deploy-the-template"></a>部署模板

使用此模板，将部署包含队列的服务总线命名空间。

[服务总线队列](service-bus-queues-topics-subscriptions.md#queues)为一个或多个竞争使用方提供先入先出 (FIFO) 消息传送方式。

若要自动运行部署，请单击以下按钮：为部署创建新的资源组，以便稍后可以轻松进行清理。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署到 Azure":::](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="validate-the-deployment"></a>验证部署

1. 选择顶部的“通知”，查看部署状态。 等待部署成功。 然后，在通知消息中选择“转到资源组”，导航到包含服务总线命名空间的资源组的页面。 

    :::image type="content" source="./media/service-bus-resource-manager-namespace-queue/notification.png" alt-text="来自部署的通知":::
2. 确认在资源列表中看到了服务总线命名空间。 

    :::image type="content" source="./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png" alt-text="资源组 - 命名空间":::
3. 从列表中选择命名空间以查看“服务总线命名空间”页。 

## <a name="clean-up-resources"></a>清理资源

1. 在 Azure 门户中，导航到资源组的“资源组”页。
2. 从工具栏选择 **删除资源组**。 
3. 键入资源组的名称，然后选择“删除”。 

    :::image type="content" source="./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png" alt-text="资源组 - 删除":::

## <a name="next-steps"></a>后续步骤

请参阅以下主题，其中介绍了如何创建命名空间/队列的授权规则：

[使用 ARM 模板为命名空间和队列创建服务总线授权规则](service-bus-resource-manager-namespace-auth-rule.md)

查看以下文章了解如何管理这些资源：

* [使用 PowerShell 管理服务总线](service-bus-manage-with-ps.md)
* [使用服务总线 Explorer 管理服务总线资源](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://github.com/Azure/azure-quickstart-templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md

<!-- Update_Description: update meta properties, wording update, update link -->