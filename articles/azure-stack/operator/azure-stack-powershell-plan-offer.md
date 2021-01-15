---
title: 使用 PowerShell 管理 Azure Stack Hub 中的订阅、计划和套餐
description: 如何使用 PowerShell 管理 Azure Stack Hub 中的订阅、计划和套餐。
author: WenJason
ms.topic: how-to
origin.date: 12/18/2020
ms.date: 01/11/2021
ms.author: v-jay
ms.lastreviewed: 12/18/2020
ms.reviewer: bganapa
ms.openlocfilehash: e0a33fb6ed881d1a91c4ac321dd83179e46b3849
ms.sourcegitcommit: 3f54ab515b784c9973eb00a5c9b4afbf28a930a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97894752"
---
# <a name="use-powershell-to-manage-subscriptions-plans-and-offers-in-azure-stack-hub"></a>使用 PowerShell 管理 Azure Stack Hub 中的订阅、计划和套餐

你可以使用 PowerShell，通过使用套餐、计划和订阅来配置并交付服务。 使用 PowerShell。 有关在 Azure Stack Hub 上使用 PowerShell 进行设置的说明，请参阅[安装适用于 Azure Stack Hub 的 PowerShell Az 模块](powershell-install-az-module.md)。 若要了解如何使用 PowerShell 连接到 Azure Stack Hub，请参阅[使用 PowerShell 连接到 Azure Stack Hub](azure-stack-powershell-configure-admin.md)。

在开始之前，请验证是否已加载 Azure Stack Hub PowerShell 模块。 在 PowerShell 控制台中，键入 `Import-Module AzureStack`。

## <a name="create-a-plan"></a>创建计划

在创建计划时，需要有配额。 你可以使用现有的配额，或者也可以创建新配额。 例如，若要创建存储、计算和网络配额，可以使用 [New-AzsStorageQuota](https://docs.microsoft.com/powershell/module/azs.storage.admin/new-azsstoragequota)、[New-AzsComputeQuota](https://docs.microsoft.com/powershell/module/azs.compute.admin/new-azscomputequota) 和 [New-AzsNetworkQuota](https://docs.microsoft.com/powershell/module/azs.network.admin/new-azsnetworkquota) cmdlet：

```powershell
$serviceQuotas  = @()
$serviceQuotas += (New-AzsStorageQuota -Name "Example storage quota with defaults").Id
$serviceQuotas += (New-AzsComputeQuota -Name "Example compute quota with defaults").Id
$serviceQuotas += (New-AzsNetworkQuota -Name "Example network quota with defaults").Id
```

若要创建或更新基本计划或加载项计划，请使用 [New-AzsPlan](https://docs.microsoft.com/powershell/module/azs.subscriptions.admin/new-azsplan)。

```powershell
$testPlan = New-AzsPlan -Name "testplan" -ResourceGroupName "testrg" -QuotaIds $serviceQuotas -Description "Test plan"
```

## <a name="create-an-offer"></a>创建产品

若要创建套餐，请使用 [New-AzsOffer](https://docs.microsoft.com/powershell/module/azs.subscriptions.admin/new-azsoffer)。

```powershell
New-AzsOffer -Name "testoffer" -ResourceGroupName "testrg" -BasePlanIds @($testPlan.Id)
```

在具有了套餐之后，你可以将计划添加到该套餐。 请使用 [Add-AzsPlanToOffer](https://docs.microsoft.com/powershell/module/azs.subscriptions.admin/add-azsplantooffer)。 -PlanLinkType 参数用于区分计划类型。

```powershell
Add-AzsPlanToOffer -PlanName "addonplan" -PlanLinkType Addon -OfferName "testoffer" -ResourceGroupName "testrg" -MaxAcquisitionCount 18
```

如果需要更改套餐的状态，请使用 [Set-AzsOffer](https://docs.microsoft.com/powershell/module/azs.subscriptions.admin/set-azsoffer) cmdlet。

```powershell
$offer = Get-AzsAdminManagedOffer -Name "testoffer" -ResourceGroupName "testrg"
$offer.state = "Public"
$offer | Set-AzsOffer -Confirm:$false
```

## <a name="create-subscription-to-an-offer"></a>创建对套餐的订阅

创建某个套餐后，用户需要订阅此套餐才能使用此套餐。 用户可通过两种方式订阅套餐：

* 作为云操作员，你可以为用户创建订阅。 创建的订阅可用于公共和专用套餐。
* 作为用户，你可以订阅公共套餐。

若要以云操作员身份为用户创建订阅，请使用 [New-AzsUserSubscription](https://docs.microsoft.com/powershell/module/azs.subscriptions.admin/new-azsusersubscription)。

```powershell
New-AzsUserSubscription -Owner "user@contoso.com" -DisplayName "User subscription" -OfferId "/subscriptions/<Subscription ID>/resourceGroups/testrg/providers/Microsoft.Subscriptions.Admin/offers/testoffer"
```

若要以用户身份订阅公共套餐，请使用 [New-AzsSubscription](https://docs.microsoft.com/powershell/module/azs.subscriptions/new-azssubscription)。 *New-AzsSubscription* 需要连接到用户 Azure 资源管理器环境。 请使用[使用 PowerShell 连接到 Azure Stack Hub](azure-stack-powershell-configure-admin.md) 中的步骤，但使用用户 Azure 资源管理器终结点。 例如，`Add-AzEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"`。

```powershell
$testOffer = Get-AzsOffer | Where-Object Name -eq "testoffer"
New-AzsSubscription -OfferId "User subscription" -OfferId $testOffer.Id -DisplayName "My subscription"
```

## <a name="delete-quotas-plans-offers-and-subscriptions"></a>删除配额、计划、套餐和订阅

PowerShell 有配套的 cmdlet 可用于删除 Azure Stack Hub 配额、计划、套餐和订阅。 下面显示了每种删除操作的示例。

请使用 [Remove-AzsUserSubscription](https://docs.microsoft.com/powershell/module/azs.subscriptions.admin/remove-azsusersubscription) 从套餐中删除订阅。

```powershell
Remove-AzsUserSubscription -TargetSubscriptionId "c90173b1-de7a-4b1d-8600-b8325ca1eab1e"
```

若要从套餐中删除计划，请使用 [Remove-AzsPlanFromOffer](https://docs.microsoft.com/powershell/module/azs.subscriptions.admin/remove-azsplanfromoffer)。

```powershell
Remove-AzsPlanFromOffer -PlanName "addonplan" -PlanLinkType Addon -OfferName "testoffer" -ResourceGroupName "testrg"
Remove-AzsPlanFromOffer -PlanName "testplan" -PlanLinkType Base -OfferName "testoffer" -ResourceGroupName "testrg"
```

请使用 [Remove-AzsPla](https://docs.microsoft.com/powershell/module/azs.subscriptions.admin/remove-azsplan) 来删除计划。

```powershell
Remove-AzsPlan -Name "testplan" -ResourceGroupName "testrg"
```

请使用 [Remove-AzsOffer](https://docs.microsoft.com/powershell/module/azs.subscriptions.admin/remove-azsoffer) 来删除套餐。

```powershell
Remove-AzsOffer -Name "testoffer" -ResourceGroupName "testrg"
```

若要删除配额，请使用 [Remove-AzsStorageQuota](https://docs.microsoft.com/powershell/module/azs.storage.admin/remove-azsstoragequota)、[Remove-AzsComputeQuota](https://docs.microsoft.com/powershell/module/azs.compute.admin/remove-azscomputequota) 和 [Remove-AzsNetworkQuota](https://docs.microsoft.com/powershell/module/azs.network.admin/remove-azsnetworkquota)。

```powershell
Remove-AzsStorageQuota -Name "Example storage quota with defaults"
Remove-AzsComputeQuota -Name "Example compute quota with defaults"
Remove-AzsNetworkQuota -Name "Example network quota with defaults"
```

## <a name="next-steps"></a>后续步骤

- [在 Azure Stack Hub 中管理更新](./azure-stack-updates.md)
