---
title: Azure PowerShell 脚本示例 - 创建 WAF 自定义规则
description: Azure PowerShell 脚本示例 - 创建 Web 应用程序防火墙自定义规则
author: vhorne
ms.service: application-gateway
ms.topic: sample
ms.date: 11/16/2020
ms.author: v-junlch
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3599fda12402373875b54f7e6d5ce04d0c5e4e65
ms.sourcegitcommit: f436acd1e2a0108918a6d2ee9a1aac88827d6e37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96508950"
---
# <a name="create-web-application-firewall-waf-custom-rules-with-azure-powershell"></a>使用 Azure PowerShell 创建 Web 应用程序防火墙 (WAF) 自定义规则

此脚本将创建使用自定义规则的应用程序网关 Web 应用程序防火墙。 如果请求标头包含用户代理 *evilbot*，该自定义规则会阻止流量。

## <a name="prerequisites"></a>必备条件

### <a name="azure-powershell-module"></a>Azure PowerShell 模块

如果选择在本地安装并使用 Azure PowerShell，则此脚本需要安装 Azure PowerShell 模块 2.1.0 或更高版本。

1. 要查找版本，请运行 `Get-Module -ListAvailable Az`。 如果需要升级，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)。
2. 若要创建与 Azure 的连接，请运行 `Connect-AzAccount -Environment AzureChinaCloud`。

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="sample-script"></a>示例脚本

```powershell
#Set up variables
$rgname = "CustomRulesTest"
$location = "China North 2"
$appgwName = "WAFCustomRules"

#Create a Resource Group
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location

#Create a VNet
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)

#Create a Static Public VIP
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard

#Create pool and frontend port
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.chinanorth2.chinacloudapp.cn, testbackend2.chinanorth2.chinacloudapp.cn
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 80

#Create a listener, http setting, rule, and autoscale
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01
$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool
$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3
$sku = New-AzApplicationGatewaySku -Name WAF_v2 -Tier WAF_v2

#Create the custom rule and apply it to WAF policy
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestHeaders -Selector User-Agent
$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "evilbot" -Transform Lowercase -NegationCondition $False  
$rule = New-AzApplicationGatewayFirewallCustomRule -Name blockEvilBot -Priority 2 -RuleType MatchRule -MatchCondition $condition -Action Block
$policy = New-AzApplicationGatewayFirewallPolicySetting -Mode "Prevention"
$wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name wafPolicy -ResourceGroup $rgname -Location $location -CustomRule $rule -PolicySetting $policy

#Create the Application Gateway
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -FirewallPolicy $wafPolicy
```

## <a name="clean-up-deployment"></a>清理部署

运行以下命令来删除资源组、应用程序网关和所有相关资源。

```powershell
Remove-AzResourceGroup -Name CustomRulesTest
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建部署。 表中的每一项均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 创建子网配置。 |
| [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) | 使用子网配置创建虚拟网络。 |
| [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) | 创建应用程序网关的公共 IP 地址。 |
| [New-AzApplicationGatewayIPConfiguration](https://docs.microsoft.com/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | 创建将子网与应用程序网关相关联的配置。 |
| [New-AzApplicationGatewayFrontendIPConfig](https://docs.microsoft.com/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | 创建为应用程序网关分配公共 IP 地址的配置。 |
| [New-AzApplicationGatewayFrontendPort](https://docs.microsoft.com/powershell/module/az.network/new-azapplicationgatewayfrontendport) | 分配用于访问应用程序网关的端口。 |
| [New-AzApplicationGatewayBackendAddressPool](https://docs.microsoft.com/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | 创建应用程序网关的后端池。 |
| [New-AzApplicationGatewayBackendHttpSettings](https://docs.microsoft.com/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | 配置后端池的设置。 |
| [New-AzApplicationGatewayHttpListener](https://docs.microsoft.com/powershell/module/az.network/new-azapplicationgatewayhttplistener) | 创建侦听器。 |
| [New-AzApplicationGatewayRequestRoutingRule](https://docs.microsoft.com/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | 创建路由规则。 |
| [New-AzApplicationGatewaySku](https://docs.microsoft.com/powershell/module/az.network/new-azapplicationgatewaysku) | 指定应用程序网关的层和容量。 |
| [New-AzApplicationGateway](https://docs.microsoft.com/powershell/module/az.network/new-azapplicationgateway) | 创建应用程序网关。 |
|[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组及其中包含的所有资源。 |
|[New-AzApplicationGatewayAutoscaleConfiguration](https://docs.microsoft.com/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|为应用程序网关创建自动缩放配置。|
|[New-AzApplicationGatewayFirewallMatchVariable](https://docs.microsoft.com/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|为防火墙条件创建匹配变量。|
|[New-AzApplicationGatewayFirewallCondition](https://docs.microsoft.com/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|为自定义规则创建匹配条件。|
|[New-AzApplicationGatewayFirewallCustomRule](https://docs.microsoft.com/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|为应用程序网关防火墙策略创建新的自定义规则。|
|[New-AzApplicationGatewayFirewallPolicy](https://docs.microsoft.com/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|创建应用程序网关防火墙策略。|
|[New-AzApplicationGatewayWebApplicationFirewallConfiguration](https://docs.microsoft.com/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|创建应用程序网关的 WAF 配置。|

## <a name="next-steps"></a>后续步骤

- 有关 WAF 自定义规则的详细信息，请参阅 [Web 应用程序防火墙的自定义规则](../custom-waf-rules-overview.md)
- 有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/)。
- 可以在 [Azure 应用程序网关文档](../powershell-samples.md)中找到其他应用程序网关 PowerShell 脚本示例。

