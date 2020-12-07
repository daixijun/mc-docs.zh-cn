---
title: 创建 Log Analytics 工作区 - Azure PowerShell
description: Azure PowerShell 脚本示例 - 创建 Log Analytics 工作区
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: v-johya
ms.date: 12/01/2020
origin.date: 09/07/2017
ms.openlocfilehash: 4d747354a98ae1032feebfdedc78fd9bf6d682d6
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96432601"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>使用 PowerShell 创建 Log Analytics 工作区

利用此脚本，可使用 Azure Log Analytics 工作区快速启动和运行；必须使用此工作区才能开始收集、分析和在数据上执行操作。  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="sample-script"></a>示例脚本

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
#Variables for common values
$ResourceGroupName = "ResourceGroup01"
$SubscriptionID = "SubscriptionID"
$WorkspaceName = "DefaultWorkspace-" + (Get-Random -Maximum 99999) + "-" + $ResourceGroupName
$Location = "chinaeast2"

# Stop the script if any errors occur
$ErrorActionPreference = "Stop"

# Connect to the current Azure account
Write-Output "Pulling Azure account credentials..."

# Login to Azure account
$Account = Add-AzAccount

# If a subscriptionID has not been provided, select the first registered to the account
if ([string]::IsNullOrEmpty($SubscriptionID)) {
   
    # Get a list of all subscriptions
    $Subscription =  Get-AzSubscription

    # Get the subscription ID
    $SubscriptionID = (($Subscription).SubscriptionId | Select -First 1).toString()

    # Get the tenant id for this subscription
    $TenantID = (($Subscription).TenantId | Select -First 1).toString()

} else {

    # Get a reference to the current subscription
    $Subscription = Get-AzSubscription -SubscriptionId $SubscriptionID
    # Get the tenant id for this subscription
    $TenantID = $Subscription.TenantId
}

# Set the active subscription
$null = Set-AzContext -SubscriptionID $SubscriptionID

# Check that the resource group is valid
$null = Get-AzResourceGroup -Name $ResourceGroupName
# Create a new Log Analytics workspace if needed
try {

    $Workspace = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName  -ErrorAction Stop
    $ExistingtLocation = $Workspace.Location
    Write-Output "Workspace named $WorkspaceName in region $ExistingLocation already exists."
    Write-Output "No further action required, script quitting."

} catch {

    Write-Output "Creating new workspace named $WorkspaceName in region $Location..."
    # Create the new workspace for the given name, region, and resource group
    $Workspace = New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroupName

}


```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令在订阅中创建新的 Log Analytics 工作区。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [Get-AzOperationalInsightsWorkspace](https://docs.microsoft.com/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | 获取现有工作区的相关信息。 |
| [New-AzOperationalInsightsWorkspace](https://docs.microsoft.com/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | 在指定的资源组和位置中创建一个工作区。 |


## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/)。


