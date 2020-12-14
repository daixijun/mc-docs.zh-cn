---
title: 如何使用 Azure PowerShell 部署 Azure Spring Cloud
description: 如何使用 Azure PowerShell 部署 Azure Spring Cloud
author: bmitchell287
ms.author: v-junlch
ms.topic: conceptual
ms.service: spring-cloud
ms.devlang: azurepowershell
ms.date: 11/30/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 866b25d90f23d345083b6eba910ae446f904b715
ms.sourcegitcommit: f436acd1e2a0108918a6d2ee9a1aac88827d6e37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509092"
---
# <a name="deploy-azure-spring-cloud-with-azure-powershell"></a>使用 Azure PowerShell 部署 Azure Spring Cloud

本文介绍如何使用 [Az.SpringCloud](https://docs.microsoft.com/powershell/module/Az.SpringCloud) PowerShell 模块创建 Azure Spring Cloud 实例。

## <a name="requirements"></a>要求

* 如果还没有 Azure 订阅，可以在开始前[创建一个帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > 尽管 Az.SpringCloud PowerShell 模块为预览版，但你需要使用 `Install-Module` cmdlet 单独安装它。 此 PowerShell 模块正式发布后，它会包含在将来的 Az PowerShell 模块发行版中，并在 Azure Cloud Shell 中默认提供。

  ```azurepowershell
  Install-Module -Name Az.SpringCloud
  ```

* 如果有多个 Azure 订阅，请选择应当计费的资源所在的相应订阅。 使用 [Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) cmdlet 选择特定订阅。

  ```azurepowershell
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) cmdlet 创建 [Azure 资源组](../azure-resource-manager/management/overview.md)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。

以下示例在指定位置创建具有指定名称的资源组。

```azurepowershell
New-AzResourceGroup -Name <resource group name> -Location chinanorth
```

## <a name="provision-a-new-instance-of-azure-spring-cloud"></a>预配 Azure Spring Cloud 的新实例

若要创建 Azure Spring Cloud 的新实例，请使用 [New-AzSpringCloud](https://docs.microsoft.com/powershell/module/az.springcloud/new-azspringcloud) cmdlet。 以下示例在先前创建的资源组中创建具有指定名称的 Azure Spring Cloud 服务。

```azurepowershell
New-AzSpringCloud -ResourceGroupName <resource group name> -name <service instance name> -Location chinanorth
```

## <a name="create-a-new-azure-spring-cloud-app"></a>创建新的 Azure Spring Cloud 应用

若要创建新应用，请使用 [New-AzSpringCloudApp](https://docs.microsoft.com/powershell/module/az.springcloud/new-azspringcloudapp) cmdlet。 以下示例创建名为 `gateway` 的 Azure Spring Cloud 应用。

```azurepowershell
New-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="create-a-new-azure-spring-cloud-deployment"></a>创建新的 Azure Spring Cloud 部署

若要创建新部署，请使用 [New-AzSpringCloudAppDeployment](https://docs.microsoft.com/powershell/module/az.springcloud/new-azspringcloudappdeployment) cmdlet。 以下示例为 `gateway` 应用创建名为 `default` 的 Azure Spring Cloud 部署。

```azurepowershell
New-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -Name <service instance name> -AppName gateway -DeploymentName default
```

## <a name="get-an-azure-spring-cloud-service"></a>获取 Azure Spring Cloud 服务

若要获取 Azure Spring Cloud 服务及其属性，请使用 [Get-AzSpringCloud](https://docs.microsoft.com/powershell/module/az.springcloud/get-azspringcloud) cmdlet。 以下示例检索有关指定的 Azure Spring Cloud 服务的信息。

```azurepowershell
Get-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

## <a name="get-an-azure-spring-cloud-app"></a>获取 Azure Spring Cloud 应用

若要获取 Azure Spring Cloud 应用及其属性，请使用 [Get-AzSpringCloudApp](https://docs.microsoft.com/powershell/module/az.springcloud/get-azspringcloudapp) cmdlet。 以下示例检索有关 `gateway` Spring Cloud 应用的信息。

```azurepowershell
Get-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="get-an-azure-spring-cloud-deployment"></a>获取 Azure Spring Cloud 部署

若要获取 Azure Spring Cloud 部署及其属性，请使用 [Get-AzSpringCloudAppDeployment](https://docs.microsoft.com/powershell/module/az.springcloud/get-azspringcloudappdeployment) cmdlet。 以下示例检索有关 `default` Spring Cloud 部署的信息。

```azurepowershell
Get-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要使用本文中创建的资源，可以运行以下示例将其删除。

### <a name="delete-an-azure-spring-cloud-deployment"></a>删除 Azure Spring Cloud 部署

若要删除 Azure Spring Cloud 部署，请使用 [Remove-AzSpringCloudAppDeployment](https://docs.microsoft.com/powershell/module/az.springcloud/remove-azspringcloudappdeployment) cmdlet。 以下示例为指定的服务和应用删除名为 `default` 的 Azure Spring Cloud 应用部署。

```azurepowershell
Remove-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

### <a name="delete-an-azure-spring-cloud-app"></a>删除 Azure Spring Cloud 应用

若要删除 Spring Cloud 应用，请使用 [Remove-AzSpringCloudApp](https://docs.microsoft.com/powershell/module/Az.SpringCloud/remove-azspringcloudapp) cmdlet。 以下示例删除指定服务和资源组中的 `gateway` 应用。

```azurepowershell
Remove-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

### <a name="delete-an-azure-spring-cloud-service"></a>删除 Azure Spring Cloud 服务

若要删除 Azure Spring Cloud 服务，请使用 [Remove-AzSpringCloud](https://docs.microsoft.com/powershell/module/Az.SpringCloud/remove-azspringcloud) cmdlet。 以下示例删除指定的 Azure Spring Cloud 服务。

```azurepowershell
Remove-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

### <a name="delete-the-resource-group"></a>删除资源组

> [!CAUTION]
> 以下示例删除指定的资源组及其包含的所有资源。
> 如果指定的资源组中存在本文范围外的资源，这些资源也会被删除。

```azurepowershell
Remove-AzResourceGroup -Name <resource group name>
```

## <a name="next-steps"></a>后续步骤

[Azure Spring Cloud 开发人员资源](spring-cloud-resources.md)。

