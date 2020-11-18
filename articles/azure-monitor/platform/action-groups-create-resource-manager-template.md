---
title: 使用资源管理器模板创建操作组
description: 了解如何使用 Azure 资源管理器模板创建操作组。
author: Johnnytechn
services: azure-monitor
origin.date: 02/16/2018
ms.topic: conceptual
ms.date: 11/02/2020
ms.author: v-johya
ms.subservice: alerts
ms.openlocfilehash: 728ff5b59842dc3a22646e386175d6f379931eb8
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94328785"
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>使用资源管理器模板创建操作组
本文说明如何使用 [Azure 资源管理器模板](../../azure-resource-manager/templates/template-syntax.md)配置操作组。 使用模板，可以自动设置可以在某些类型的警报中重复使用的操作组。 这些操作组可确保警报触发时所有相应的当事方可以收到通知。

基本步骤如下：

1. 以 JSON 文件的形式创建一个描述如何创建操作组的模板。

2. 使用[任意部署方法](../../azure-resource-manager/templates/deploy-powershell.md)部署模板。

## <a name="resource-manager-templates-for-an-action-group"></a>用于操作组的资源管理器模板

若要使用资源管理器模板创建操作组，需要创建 `Microsoft.Insights/actionGroups` 类型的资源。 然后，填充所有相关属性。 以下是两个用于创建操作组的示例模板。

第一个模板介绍如何为操作组（其中的操作定义在模板中进行了硬编码）创建资源管理器模板。 第二个模板介绍如何创建在部署模板时以输入参数形式接受 Webhook 配置信息的模板。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com"
          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com"
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1"
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    },
    "webhookReceiverName": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service Name."
      }
    },    
    "webhookServiceUri": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    }    
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
        ],
        "emailReceivers": [
        ],
        "webhookReceivers": [
          {
            "name": "[parameters('webhookReceiverName')]",
            "serviceUri": "[parameters('webhookServiceUri')]"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupResourceId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```


## <a name="next-steps"></a>后续步骤
* 详细了解[操作组](./action-groups.md)。
* 详细了解[警报](alerts-overview.md)。
* 了解如何[使用资源管理器模板添加警报](./alerts-activity-log.md)。


