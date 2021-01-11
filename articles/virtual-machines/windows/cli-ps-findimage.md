---
title: 查找并使用 Azure 市场映像和计划
description: 使用 Azure PowerShell 来查找并使用发布服务器、产品/服务、SKU 以及市场 VM 映像的版本和计划信息。
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
origin.date: 12/07/2020
author: rockboyfor
ms.date: 01/04/2021
ms.testscope: yes
ms.testdate: 08/31/2020
ms.author: v-yeche
ms.openlocfilehash: 8d79f42457f425f536924283ced4755824e87779
ms.sourcegitcommit: b4fd26098461cb779b973c7592f951aad77351f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857126"
---
# <a name="find-and-use-azure-marketplace-vm-images-with-azure-powershell"></a>使用 Azure PowerShell 查找并使用 Azure 市场 VM 映像     

本文介绍如何使用 Azure PowerShell 在 Azure 市场中查找 VM 映像。 然后，可以在创建 VM 时指定市场映像。

你还可以使用 [Azure 市场](https://market.azure.cn/marketplace/apps)店面、[Azure 门户](https://portal.azure.cn)或 [Azure CLI](../linux/cli-ps-findimage.md) 浏览可用的映像和产品/服务。 

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="create-a-vm-from-vhd-with-plan-information"></a>使用计划信息从 VHD 创建 VM

如果你有使用 Azure 市场映像创建的现有 VHD，则在从该 VHD 创建新的 VM 时，可能需要提供购买计划信息。

如果你仍有原始 VM 或从同一映像创建的其他 VM，则可以使用 Get-AzVM 从其中获取计划名称、发布者和产品信息。 此示例获取 myResourceGroup 资源组中名为 myVM 的 VM，然后显示购买计划信息 。

```powershell
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

如果在删除原始 VM 之前未获得计划信息，则可提交[支持请求](https://support.azure.cn/support/support-azure/)。 他们将需要 VM 名称、订阅 ID 和删除操作的时间戳。

若要使用 VHD 创建 VM，请参阅[从专用 VHD 创建 VM](create-vm-specialized.md) 一文，并添加一行，使用 [Set-AzVMPlan](https://docs.microsoft.com/powershell/module/az.compute/set-azvmplan) 将计划信息添加到 VM 配置，如下所示：

```powershell
$vmConfig = Set-AzVMPlan `
   -VM $vmConfig `
   -Publisher "publisherName" `
   -Product "productName" `
   -Name "planName"
```

## <a name="create-a-new-vm-from-a-marketplace-image"></a>从市场映像创建新 VM

如果已有要使用的映像的相关信息，可以将该信息传递到 [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) cmdlet，以将映像信息添加到 VM 配置。 请参阅后续部分，了解如何搜索和列出市场中可用的映像。

某些付费映像还要求使用 [Set-AzVMPlan](https://docs.microsoft.com/powershell/module/az.compute/set-azvmplan) 提供购买计划信息。 

<!--ADJUST THE publisherName-->

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace image
$offerName = "windows-data-science-vm"
$skuName = "windows2016"
$version = "19.01.14"
$publisherName = "microsoft-ads"

$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version

# Set the Marketplace plan information, if needed
$productName = "windows-data-science-vm"
$planName = "windows2016"
$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

...
```

然后将 VM 配置与其他配置对象一起传递给 `New-AzVM` cmdlet。 有关将 VM 配置与 PowerShell 配合使用的详细示例，请参阅此[脚本](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine/create-vm-detailed/create-windows-vm-detailed.ps1)。

如果收到有关接受映像条款的消息，请参阅本文后面的[接受条款](#accept-the-terms)部分。

## <a name="list-images"></a>列出映像

在某个位置查找映像的一种方法是按顺序运行 [Get-AzVMImagePublisher](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher)、[Get-AzVMImageOffer](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer) 和 [Get-AzVMImageSku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) cmdlet：

1. 列出映像发布者。
2. 对于给定的发布者，列出其产品。
3. 对于给定的产品，列出其 SKU。

然后，对所选 SKU 运行 [Get-AzVMImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimage)，列出要部署的版本。

1. 列出发布者：

    ```powershell
    Connect-AzAccount -Environment AzureChinaCloud
    $locName="<Azure location, such as China North>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. 填写你选择的发布者名称并列出产品/服务：

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```

3. 填写你选择的产品/服务名称并列出 SKU：

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```

4. 填写你选择的 SKU 名称并获取映像版本：

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```

从 `Get-AzVMImage` 命令的输出中，可以选择要部署新虚拟机的版本映像。

以下示例显示了命令及其输出的完整序列：

```powershell
$locName="China North"
Get-AzVMImagePublisher -Location $locName | Select PublisherName
```

部分输出：

<!--MOONCCAKE: CUSTOMIZE-->

```
PublisherName
-------------
...
360-cn
A10Networks
a10networks-cn
afajr-cn
aibaby
aigauss
airdoc
airdoc-cn
alauda
AllMobilize
alteonva
Antshares
arcblock-cn
arctron
array_networks
array_networks-cn
AsiaInfo.DeepSecurity
attittu-cn
AzureChinaMarketplace
AzureDatabricks
...
```

<!--MOONCCAKE: CUSTOMIZE-->

对于“MicrosoftWindowsServer”发布者：

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
```

输出：

<!--MOONCAKE CUSTOMIZE: invalid on Windows-HUB-->

```
Offer
-----
WindowsServer
WindowsServerSemiAnnual
```

<!--MOONCAKE CUSTOMIZE: invalid on Windows-HUB-->

对于“WindowsServer”产品/服务：

```powershell
$offerName="WindowsServer"
Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
```

部分输出：

<!--MOONCAKE CUSTOMIZE-->

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2008-R2-SP1-zhcn
2012-Datacenter
2012-datacenter-gensecond
2012-Datacenter-smalldisk
2012-datacenter-smalldisk-g2
2012-Datacenter-zhcn
2012-datacenter-zhcn-g2
2012-R2-Datacenter
2012-r2-datacenter-gensecond
2012-R2-Datacenter-smalldisk
2012-r2-datacenter-smalldisk-g2
2012-R2-Datacenter-zhcn
2012-r2-datacenter-zhcn-g2
2016-Datacenter
2016-datacenter-gensecond
2016-datacenter-gs
2016-Datacenter-Server-Core
2016-datacenter-server-core-g2
2016-Datacenter-Server-Core-smalldisk
2016-datacenter-server-core-smalldisk-g2
2016-Datacenter-smalldisk
2016-datacenter-smalldisk-g2
2016-Datacenter-with-Containers
2016-datacenter-with-containers-g2
2016-datacenter-with-containers-gs
2016-Datacenter-zhcn
2016-datacenter-zhcn-g2
2016-Nano-Server
2019-Datacenter
2019-Datacenter-Core
2019-datacenter-core-g2
2019-Datacenter-Core-smalldisk
2019-datacenter-core-smalldisk-g2
2019-Datacenter-Core-with-Containers
...
```

然后，对于 *2019-Datacenter* SKU：

<!--MOONCAKE CUSTOMIZE-->

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
```

现在可以将所选发布者、产品/服务、SKU 和版本合并到 URN 中（由“:”分隔的值）。 使用 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet 创建 VM 时，使用 `--image` 参数传递此 URN。 还可以将 URN 中的版本号替换为 "latest" 以获取映像的最新版本。

如果使用资源管理器模板部署 VM，请在 `imageReference` 属性中单独设置映像参数。

<!--Not Available on [template reference](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)-->

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>查看计划属性

若要查看映像的购买计划信息，请运行 `Get-AzVMImage` cmdlet。 如果输出中的 `PurchasePlan` 属性不是 `null`，则映像有条款，在以编程方式部署前需要接受该条款。  

<!--MOONCAKE CUSTOMIZE-->

例如，*Windows Server 2019 Datacenter* 映像没有附加条款，因此，`PurchasePlan` 信息为 `null`：

```powershell
$version = "2019.0.20190115"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

输出：

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/chinanorth/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : chinanorth
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

<!--MOONCAKE CUSTOMZIE: invalid *Data Science Virtual Machine - Windows 2016*-->
<!--Not Available on -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016"-->

<!--Not Available on ### Accept the terms-->
<!--Not Available on ### Deploy using purchase plan parameters-->

## <a name="next-steps"></a>后续步骤

若要使用基本映像信息通过 `New-AzVM` cmdlet 快速创建虚拟机，请参阅[使用 PowerShell 创建 Windows 虚拟机](quick-create-powershell.md)。

有关使用 Azure 市场映像在共享映像库中创建自定义映像的更多信息，请参阅[在创建映像时提供 Azure 市场购买计划信息](../marketplace-images.md)。

<!-- Update_Description: update meta properties, wording update, update link -->