---
title: 升级公共 IP 地址
titleSuffix: Azure Virtual Network
description: 将公共 IP 地址从基本 SKU 升级到标准 SKU。
services: virtual-network
documentationcenter: na
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 12/08/2020
author: rockboyfor
ms.date: 01/18/2021
ms.testscope: yes
ms.testdate: 01/18/2021
ms.author: v-yeche
ms.custom: references_regions
ms.openlocfilehash: 1f266d578aee3b5dfd201ce1bafc23ce3a5a3819
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98231157"
---
<!--Verified successfully-->
# <a name="upgrade-public-ip-addresses"></a>升级公共 IP 地址

Azure 公共 IP 地址是采用某个 SKU 创建的，该 SKU 可以是基本的，也可以是标准的，它决定了公共 IP 地址的功能的各个方面（包括分配方法、跨可用性区域的使用以及它们可以与哪些资源关联）。 

本文介绍了以下方案：
* 如何将基本 SKU 公共 IP 升级到标准 SKU 公共 IP（使用门户、PowerShell 或 CLI）
* 如何将经典 Azure 保留 IP 迁移到 Azure 资源管理器基本 SKU 公共 IP

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>将公共 IP 地址从基本 SKU 升级到标准 SKU

若要升级公共 IP，则它不得与任何资源相关联（有关如何将公共 IP 解除关联的详细信息，请查看[此页](https://docs.azure.cn/virtual-network/virtual-network-public-ip-address#view-modify-settings-for-or-delete-a-public-ip-address)）。

<!--Not Available on [availability zones](https://docs.azure.cn/availability-zones/az-overview?toc=/virtual-network/toc.json#availability-zones)-->
---
# <a name="basic-to-standard---powershell"></a>[**从基本 SKU 升级到标准 SKU - PowerShell**](#tab/option-upgrade-powershell)

下面的示例假设之前创建了一个基本 SKU 公共 IP，具体而言，是使用[此页](https://docs.azure.cn/virtual-network/create-public-ip-powershell?tabs=option-create-public-ip-basic)上提供的示例在“myResourceGroup”中创建了一个基本公共 IP“myBasicPublicIP”。

若要升级 IP，只需使用 PowerShell 执行以下命令即可。  注意，如果已静态分配了 IP 地址，则可以跳过该部分。

```powershell
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'
$newsku = 'Standard'
$pubIP = Get-AzPublicIpAddress -name $name -ResourceGroupName $rg

## This section is only needed if the Basic IP is not already set to Static ##
$pubIP.PublicIpAllocationMethod = 'Static'
Set-AzPublicIpAddress -PublicIpAddress $pubIP

## This section is for conversion to Standard ##
$pubIP.Sku.Name = $newsku
Set-AzPublicIpAddress -PublicIpAddress $pubIP
```

# <a name="basic-to-standard---cli"></a>[**从基本 SKU 升级到标准 SKU - CLI**](#tab/option-upgrade-cli)

下面的示例假设之前创建了一个基本 SKU 公共 IP，具体而言，是使用[此页](https://docs.azure.cn/virtual-network/create-public-ip-cli?tabs=option-create-public-ip-basic)上提供的示例在“myResourceGroup”中创建了一个基本公共 IP“myBasicPublicIP”。

若要升级 IP，只需使用 Azure CLI 执行以下命令即可。  注意，如果已静态分配了 IP 地址，则可以跳过该部分。

<!--CORRECT ON CLI VARIANT FORMAT-->

```azurecli
## Variables for the command ##
rg='myResourceGroup'
name='myBasicPublicIP'

## This section is only needed if the Basic IP is not already set to Static ##
az network public-ip update \
--resource-group $rg \
--name $name \
--allocation-method Static 

## This section is for conversion to Standard ##
az network public-ip update \
--resource-group $rg \
--name $name \
--sku Standard
```
---

<!--CORRECT ON CLI VARIANT FORMAT-->

## <a name="upgrade-migrate-a-classic-reserved-ip-to-a-static-public-ip"></a>将经典保留 IP 升级（迁移）到静态公共 IP

若要利用 Azure 资源管理器中的新功能，可以将现有的公共静态 IP 地址（称为保留 IP）从经典模型迁移到新式 Azure 资源管理器模型。  迁移的公共 IP 将是基本 SKU 类型。

---

# <a name="reserved-to-basic---powershell"></a>[**从保留 SKU 升级到基本 SKU - PowerShell**](#tab/option-migrate-powershell)

下面的示例假设之前在 myResourceGroup 中创建了一个经典 Azure 保留 IP“myReservedIP”。  进行迁移的另一个先决条件是确保 Azure 资源管理器订阅已针对迁移进行了注册。 [此页](https://docs.azure.cn/virtual-machines/windows/migration-classic-resource-manager-ps)上的步骤 3 和 4 详细介绍了这一点。

若要迁移保留 IP，请使用 PowerShell 执行以下命令。  注意，如果 IP 地址没有与任何服务（下面有一个名为“myService”的服务）相关联，则可以跳过该步骤。

```powershell
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$serviceName = 'myService'
Remove-AzureReservedIPAssociation -ReservedIPName $name -ServiceName $service

$validate = Move-AzureReservedIP -ReservedIPName $name -Validate
$validate.ValidationMessages
```
上一命令显示任何阻止迁移的警告和错误。 如果验证成功，则可继续执行以下步骤来准备并提交迁移：
```powershell
Move-AzureReservedIP -ReservedIPName $name -Prepare
Move-AzureReservedIP -ReservedIPName $name -Commit
```
将使用迁移的保留 IP 的名称在 Azure 资源管理器中创建一个新资源组（在上面的示例中，它将是资源组“myReservedIP-Migrated”）。

# <a name="reserved-to-basic---cli"></a>[**从保留 SKU 升级到基本 SKU - CLI**](#tab/option-migrate-cli)

下面的示例假设之前在 myResourceGroup 中创建了一个经典 Azure 保留 IP“myReservedIP”。  进行迁移的另一个先决条件是确保 Azure 资源管理器订阅已针对迁移进行了注册。 [此页](https://docs.azure.cn/virtual-machines/linux/migration-classic-resource-manager-cli)上的步骤 3 和 4 详细介绍了这一点。

若要迁移保留 IP，请使用 Azure CLI 执行以下命令。  注意，如果 IP 地址没有与任何服务（下面有一个名为“myService”的服务和部署“myDeployment”）相关联，则可以跳过该步骤。

<!--CORRECT ON CLI VARIANT FORMAT-->

```azurecli
## Variables for the command ##
name='myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
service='myService'
deployment='myDeployment'
azure network reserved-ip disassociate $name $service $deployment

azure network reserved-ip validate-migration $name
```

<!--CORRECT ON CLI VARIANT FORMAT-->

上一命令显示任何阻止迁移的警告和错误。 如果验证成功，则可继续执行以下步骤来准备并提交迁移：
```azurecli
azure network reserved-ip prepare-migration $name
azure network reserved-ip commit-migration $name
```
将使用迁移的保留 IP 的名称在 Azure 资源管理器中创建一个新资源组（在上面的示例中，它将是资源组“myReservedIP-Migrated”）。

---

## <a name="limitations"></a>限制

* 若要升级基本的公共 IP，则它不能与任何 Azure 资源相关联。  请查看[此页](https://docs.azure.cn/virtual-network/virtual-network-public-ip-address#view-modify-settings-for-or-delete-a-public-ip-address)来详细了解如何将公共 IP 解除关联。  类似地，若要迁移保留 IP，则它不能与任何云服务相关联。  请查看[此页](https://docs.azure.cn/virtual-network/remove-public-ip-address-vm)来详细了解如何将保留 IP 解除关联。  

    <!--Not Available on [availability zones](https://docs.azure.cn/availability-zones/az-overview?toc=/virtual-network/toc.json#availability-zones)-->
    
* 无法从标准 SKU 降级到基本 SKU。

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure 中的[公共 IP 地址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)，包括 SKU 类型之间的差异，以及[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)。
- 了解如何[将 Azure 公共负载均衡器从基本 SKU 升级到标准 SKU](https://docs.azure.cn/load-balancer/upgrade-basic-standard)。
- 了解[经典 Azure 保留 IP](https://docs.microsoft.com/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) 以及[将经典资源迁移到 Azure 资源管理器](https://docs.azure.cn/virtual-machines/windows/migration-classic-resource-manager-overview)。

<!-- Update_Description: new article about virtual network public ip address upgrade -->
<!--NEW.date: 01/18/2021-->