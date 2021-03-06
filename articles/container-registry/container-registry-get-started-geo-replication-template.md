---
title: 快速入门 - 创建异地复制的注册表 - Azure 资源管理器模板
description: 了解如何使用 Azure 资源管理器模板创建异地复制 Azure 容器注册表。
services: azure-resource-manager
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
origin.date: 10/06/2020
author: rockboyfor
ms.date: 11/02/2020
ms.testscope: yes
ms.testdate: 08/03/2020
ms.author: v-yeche
ms.openlocfilehash: edea85f95427ebd0f111b8a5e629bb8801895619
ms.sourcegitcommit: a1f565fd202c1b9fd8c74f814baa499bbb4ed4a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96508046"
---
<!--Verified successfully-->
# <a name="quickstart-create-a-geo-replicated-container-registry-by-using-an-arm-template"></a>快速入门：使用 ARM 模板创建异地复制容器注册表

本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）创建 Azure 容器注册表实例。 该模板会设置一个[异地复制](container-registry-geo-replication.md)注册表，使其自动在多个 Azure 区域之间同步注册表内容。 借助异地复制，可以从区域部署对映像进行近网络访问，同时提供单一管理体验。 这是[高级](container-registry-skus.md)注册表服务层级的一项功能。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../includes/azure-raw-githubusercontent-azurechinacloud-environment-notice.md)]

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署到 Azure":::](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://www.microsoft.com/china/azure/index.html?fromtype=cn)帐户。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication/)。 该模板会设置注册表和其他区域副本。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "acrName": {
      "type": "string",
      "defaultValue": "[concat('acr', uniqueString(resourceGroup().id))]",
      "minLength": 5,
      "maxLength": 50,
      "metadata": {
        "description": "Globally unique name of your Azure Container Registry"
      }
    },
    "acrAdminUserEnabled": {
      "type": "bool",
      "defaultValue": false,
      "metadata": {
        "description": "Enable admin user that has push / pull permission to the registry."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for registry home replica."
      }
    },
    "acrSku": {
      "type": "string",
      "defaultValue": "Premium",
      "allowedValues": [
        "Premium"
      ],
      "metadata": {
        "description": "Tier of your Azure Container Registry. Geo-replication requires Premium SKU."
      }
    },
    "acrReplicaLocation": {
      "type": "string",
      "metadata": {
        "description": "Short name for registry replica location."
      }
    }
  },
  "resources": [
    {
      "comments": "Container registry for storing docker images",
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('acrName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('acrSku')]",
        "tier": "[parameters('acrSku')]"
      },
      "tags": {
        "displayName": "Container Registry",
        "container.registry": "[parameters('acrName')]"
      },
      "properties": {
        "adminUserEnabled": "[parameters('acrAdminUserEnabled')]"
      }
    },
    {
      "type": "Microsoft.ContainerRegistry/registries/replications",
      "apiVersion": "2019-12-01-preview",
      "name": "[concat(parameters('acrName'), '/', parameters('acrReplicaLocation'))]",
      "location": "[parameters('acrReplicaLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
      ],
      "properties": {}
    }
  ],
  "outputs": {
    "acrLoginServer": {
      "value": "[reference(resourceId('Microsoft.ContainerRegistry/registries',parameters('acrName')),'2019-12-01-preview').loginServer]",
      "type": "string"
    }
  }
}
```

该模板中定义了以下资源：

* **Microsoft.ContainerRegistry/registries**：创建 Azure 容器注册表
    
    <!--Not Available on (https://docs.microsoft.com/azure/templates/microsoft.containerregistry/registries)-->
    
* **Microsoft.ContainerRegistry/registries/replications**：创建容器注册表副本

    <!--Not Available on (https://docs.microsoft.com/azure/templates/microsoft.containerregistry/registries/replications)-->

可以在[快速入门模板库](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular)中找到更多 Azure 容器注册表模板示例。

## <a name="deploy-the-template"></a>部署模板

<!--MOONCAKE CUSTOMIZE TILL ON 10/30/2020-->

1. 选择下图登录到 Azure 并打开一个模板。

    [:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署到 Azure":::](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

1. 选择或输入以下值。

    * 订阅：选择一个 Azure 订阅。
    * **资源组**：选择“新建”，为资源组输入一个独一无二的名称，然后选择“确定”。 
    * **位置**：选择资源组的位置。 示例：**中国北部**。
    * **Acr 名称**：接受为注册表生成的名称，或者输入一个名称。 它必须全局唯一。
    * **已启用 Acr 管理员用户**：接受默认值。
    * **位置**：接受为注册表的主副本生成的位置，或输入一个位置，例如“中国北部”。 
    * **Acr Sku**：接受默认值。
    * **Acr 副本位置**：使用区域的短名称输入注册表副本的位置。 该位置必须与主注册表的位置不同。 示例：chinanorth。
    * **我同意上述条款和条件**：选中。

        :::image type="content" source="media/container-registry-get-started-geo-replication-template/template-properties.png" alt-text="模板属性":::

 3. 如果接受条款和条件，请选择“购买”。 成功创建注册表后，你会收到通知：

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/deployment-notification.png" alt-text="门户通知":::

 使用 Azure 门户部署模板。 除了 Azure 门户之外，还可以使用 Azure PowerShell、Azure CLI 和 REST API。 若要了解其他部署方法，请参阅[部署模板](../azure-resource-manager/templates/deploy-cli.md)。

<!--MOONCAKE CUSTOMIZE TILL ON 10/30/2020-->

## <a name="review-deployed-resources"></a>查看已部署的资源

使用 Azure 门户或诸如 Azure CLI 之类的工具来查看容器注册表的属性。

1. 在门户中，搜索“容器注册表”，然后选择所创建的容器注册表。

1. 在“概述”页上，记下注册表的“登录服务器” 。 使用 Docker 标记映像并将其推送到注册表时，请使用此 URI。 有关信息，请参阅[使用 Docker CLI 推送第一个映像](container-registry-get-started-docker-cli.md)。

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-overview.png" alt-text="注册表概述":::

1. 在“复制”页上，确认主副本和通过该模板添加的副本的位置。 如果需要，可在此页上添加更多副本。

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-replications.png" alt-text="注册表复制":::

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、注册表和注册表副本，请将其删除。 为此，请访问 Azure 门户，选择包含注册表的资源组，然后选择“删除资源组”。

删除资源组

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用 ARM 模板创建了 Azure 容器注册表，并在其他位置配置了注册表副本。 请继续阅读 Azure 容器注册表教程，以更深入地了解 ACR。

> [!div class="nextstepaction"]
> [Azure 容器注册表教程](container-registry-tutorial-prepare-registry.md)

有关引导你完成模板创建过程的分步教程，请参阅：

> [!div class="nextstepaction"]
> 创建和部署你的第一个 ARM 模板[

<!-- Update_Description: update meta properties, wording update, update link -->