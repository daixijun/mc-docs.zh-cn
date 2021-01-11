---
title: Azure 自动化常见问题解答 | Microsoft Docs
description: 本文提供了有关 Azure 自动化的常见问题解答。
services: automation
ms.subservice: ''
ms.topic: conceptual
origin.date: 12/17/2020
ms.date: 01/04/2021
ms.openlocfilehash: ad8a4cf2ccb24385167a6aff91f18c6ffb3d4465
ms.sourcegitcommit: cf3d8d87096ae96388fe273551216b1cb7bf92c0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97830240"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure 自动化常见问题解答

本常见问题解答文章列出了有关 Azure 自动化的常见问题。 如果对其功能还有其他任何问题，请转到论坛并发布问题。 如果某个问题经常被问到，我们会将其添加到本文中，以便可以快捷轻松地找到该问题。

## <a name="update-management"></a>更新管理

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>能否阻止意外的 OS 级别升级？

在某些 Linux 版本（例如 Red Hat Enterprise Linux）中，可以通过包执行 OS 级别的升级。 这可能会导致运行更新管理并更改 OS 版本号。 由于更新管理使用相同的方法来更新管理员将在 Linux 计算机本地使用的包，因此，此行为是有意实施的。

若要避免通过更新管理部署来更新 OS 版本，可以使用“排除”功能。

在 Red Hat Enterprise Linux 中，要排除的包名称为 `redhat-release-server.x86_64`。

### <a name="why-arent-criticalsecurity-updates-applied"></a>为什么没有应用关键更新/安全更新？

将更新部署到 Linux 计算机时，可以选择更新分类。 该选项会筛选满足指定条件的更新。 部署更新时，会在计算机本地应用此筛选器。

由于更新管理在云中执行更新扩充，因此可以将更新管理中的某些更新标记为存在安全影响，但是本地计算机上不会显示该信息。 如果向 Linux 计算机应用关键更新，可能有些更新不会被标记为对该计算机具有安全影响，因此不会应用这些更新。 但是，更新管理仍可能报告该计算机不合规，因为其中包含相关更新的其他信息。

在 CentOS 的 RTM 版本上无法按更新分类部署更新。 要为 CentOS 正确部署更新，请选择所有分类以确保应用更新。 对于 SUSE，如果其他更新与 zypper（包管理器）相关或首先需要其依赖项，则仅选择“其他更新”作为分类可能会导致安装其他一些安全更新。 此行为是 zypper 的一项限制。 在某些情况下，可能需要重新运行更新部署，然后通过更新日志验证部署。

### <a name="can-i-deploy-updates-across-azure-tenants"></a>能否在 Azure 租户之间部署更新？

如果你在向“更新管理”进行报告的另一个 Azure 租户中存在需要修补的计算机，则必须使用以下解决方法来计划它们。 可以将 [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule) cmdlet 与指定的 `ForUpdateConfiguration` 参数一起使用来创建计划。 可以使用 [New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) cmdlet，并将另一个租户中的计算机传递给 `NonAzureComputer` 参数。 以下示例演示如何执行此操作。

```azurepowershell
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

