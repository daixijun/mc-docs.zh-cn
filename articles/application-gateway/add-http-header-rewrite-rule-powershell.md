---
title: 在 Azure 应用程序网关中重写 HTTP 标头
description: 本文介绍如何使用 Azure PowerShell 重写 Azure 应用程序网关中的 HTTP 标头
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/16/2020
ms.author: v-junlch
ms.openlocfilehash: 612992617c5b32008cd669a0a9cec5a57518a309
ms.sourcegitcommit: f436acd1e2a0108918a6d2ee9a1aac88827d6e37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96508970"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>重写 Azure 应用程序网关中的 HTTP 请求和响应标头 - Azure PowerShell

本文介绍如何使用 Azure PowerShell 配置[应用程序网关 v2 SKU](./application-gateway-autoscaling-zone-redundant.md) 实例，重写请求和响应中的 HTTP 标头。

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="before-you-begin"></a>准备阶段

- 若要完成本文中的步骤，需在本地运行 Azure PowerShell。 还需安装 Az 模块 1.0.0 或更高版本。 运行 `Import-Module Az` 和 `Get-Module Az`，确定已安装的版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps)（安装 Azure PowerShell 模块）。 验证 PowerShell 版本以后，请运行 `Connect-AzAccount -Environment AzureChinaCloud`，以便创建与 Azure 的连接。
- 需要有应用程序网关 v2 SKU 实例。 v1 SKU 不支持重写标头。 如果没有 v2 SKU，请在开始之前创建[应用程序网关 v2 SKU](./tutorial-autoscale-ps.md) 实例。

## <a name="create-required-objects"></a>创建所需对象

若要配置 HTTP 标头重写，需要完成以下步骤。

1. 创建 HTTP 标头重写所需的对象：

   - **RequestHeaderConfiguration**：用于指定要重写的请求标头字段，以及标头的新值。

   - **ResponseHeaderConfiguration**：用于指定要重写的响应标头字段，以及标头的新值。

   - **ActionSet**：包含前面指定的请求和响应标头的配置。

   - **条件**：一个可选配置。 重写条件评估 HTTP(S) 请求和响应的内容。 如果 HTTP(S) 请求或响应与重写条件匹配，则会发生重写操作。

     如果将多个条件关联到一个操作，仅当满足所有条件时，才会发生该操作。 换言之，操作属于逻辑 AND 运算。

   - **RewriteRule**：包含多个重写操作/重写条件的组合。

   - **RuleSequence**：一项可选配置，用于确定重写规则的执行顺序。 在一个重写集中使用多个重写规则时，此配置非常有用。 规则顺序值较小的重写规则最先运行。 如果为两个重写规则分配了相同的规则顺序值，则执行顺序是不确定的。

     如果没有显式指定 RuleSequence explicitly，则会设置默认值 100。

   - **RewriteRuleSet**：包含多个要关联到请求路由规则的重写规则。

2. 将 RewriteRuleSet 附加到路由规则。 重写配置将通过路由规则附加到源侦听器。 使用基本路由规则时，标头重写配置与源侦听器相关联，并且是全局标头重写。 使用基于路径的路由规则时，将在 URL 路径映射中定义标头重写配置。 在这种情况下，该规则只会应用到站点的特定路径区域。

可以创建多个 HTTP 标头重写集，并将每个重写集应用到多个侦听器。 但是，对于一个特定的侦听器，只能应用一个重写集。

## <a name="sign-in-to-azure"></a>登录 Azure

```azurepowershell
Connect-AzAccount -Environment AzureChinaCloud
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>指定 HTTP 标头重写规则配置

在此示例中，我们将修改重定向 URL，方法是：当 HTTP 响应中的 location 标头包含对 chinacloudsites.cn 的引用时，重写该 location 标头。 为此，我们将添加一个条件，用于评估响应中的 location 标头是否包含 chinacloudsites.cn。 我们将使用 `(https?):\/\/.*chinacloudsites\.cn(.*)$` 模式。 我们将使用 `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` 作为标头值。 该值会在 location 标头中将 *chinacloudsites.cn* 替换为 *contoso.com*。

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*chinacloudsites\.cn(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>检索应用程序网关配置

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>检索请求路由规则配置

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>使用配置更新应用程序网关以重写 HTTP 标头

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>删除重写规则

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>后续步骤

若要详细了解如何设置某些常见用例，请参阅[常见标头重写方案](./rewrite-http-headers.md)。

