---
title: Azure 资源管理器：创建单一数据库
description: 使用 Azure 资源管理器模板在 Azure SQL 数据库中创建单一数据库。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: subject-armqs sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: quickstart
author: WenJason
ms.author: v-jay
ms.reviewer: carlrab
origin.date: 06/24/2020
ms.date: 08/17/2020
ms.openlocfilehash: 5a7c571b22cee474490db90645e1cd01949dc116
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96432039"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-an-arm-template"></a>快速入门：使用 ARM 模板在 Azure SQL 数据库中创建单一数据库

在 Azure SQL 数据库中创建数据库时，创建[单一数据库](single-database-overview.md)是最快速且最简单的选项。 本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）创建单一数据库。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-database%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请[创建一个试用版订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="review-the-template"></a>查看模板

单一数据库有一组通过两个[购买模型](purchasing-models.md)中的一个定义的计算、内存和存储资源。 创建单一数据库时，还需要定义一个[服务器](logical-servers.md)来管理它并将它放置在指定区域的 [Azure 资源组](../../active-directory-b2c/overview.md)中。

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-sql-database/)。

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serverName": {
      "type": "string",
      "defaultValue": "[uniqueString('sql', resourceGroup().id)]",
      "metadata": {
        "description": "The name of the SQL logical server."
      }
    },
    "sqlDBName": {
      "type": "string",
      "defaultValue": "SampleDB",
      "metadata": {
        "description": "The name of the SQL Database."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    },
    "administratorLogin": {
      "type": "string",
      "metadata": {
        "description": "The administrator username of the SQL logical server."
      }
    },
    "administratorLoginPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The administrator password of the SQL logical server."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2019-06-01-preview",
      "name": "[parameters('serverName')]",
      "location": "[parameters('location')]",
      "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
      },
      "resources": [
        {
          "type": "databases",
          "apiVersion": "2019-06-01-preview",
          "name": "[parameters('sqlDBName')]",
          "sku": {
            "name": "Standard",
            "tier": "Standard"
          },
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Sql/servers', concat(parameters('serverName')))]"
          ]
        }
      ]
    }
  ]
}
```

该模板中定义了以下资源：

- [**Microsoft.Sql/servers**](https://docs.microsoft.com/azure/templates/microsoft.sql/servers)
- [Microsoft.Sql/servers/databases](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases)

可以在 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular)中找到更多 Azure SQL 数据库模板示例。

## <a name="deploy-the-template"></a>部署模板

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. chinaeast2)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sql-database/azuredeploy.json" -administratorLogin $adminUser -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="validate-the-deployment"></a>验证部署

若要查询数据库，请参阅[查询数据库](single-database-create-quickstart.md#query-the-database)。

## <a name="clean-up-resources"></a>清理资源

如果希望转到[后续步骤](#next-steps)，请保留此资源组、服务器和单一数据库。 后续步骤展示了如何使用各种方法连接和查询数据库。

若要删除资源组，请执行以下操作：

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>后续步骤

- 创建服务器级防火墙规则，以便通过本地或远程工具连接到单一数据库。 有关详细信息，请参阅[创建服务器级防火墙规则](firewall-create-server-level-portal-quickstart.md)。
- 创建服务器级防火墙规则后，使用多种不同的工具和语言[连接和查询](connect-query-content-reference-guide.md)数据库。
  - [使用 SQL Server Management Studio 连接和查询](connect-query-ssms.md)
  - [使用 Azure Data Studio 连接和查询](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/sql-database/toc.json)
- 若要使用 Azure CLI 创建单一数据库，请参阅 [Azure CLI 示例](az-cli-script-samples-content-guide.md)。
- 若要使用 Azure PowerShell 创建单一数据库，请参阅 [Azure PowerShell 示例](powershell-script-content-guide.md)。
- 若要了解如何创建 ARM 模板，请参阅[创建第一个模板](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)。
