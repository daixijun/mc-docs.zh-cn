---
title: Azure PowerShell 脚本示例 - 限制 Web 流量 | Microsoft Docs
description: Azure PowerShell 脚本示例 - 使用 Web 应用程序防火墙和使用 OWASP 规则限制流量的虚拟机规模集创建应用程序网关。
services: application-gateway
documentationcenter: networking
author: vhorne
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/03/2020
ms.author: v-junlch
ms.custom: mvc
ms.openlocfilehash: cdd8fc8dcdeab3d9ef5e5e8d84cc638b312ea050
ms.sourcegitcommit: f436acd1e2a0108918a6d2ee9a1aac88827d6e37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509007"
---
# <a name="restrict-web-traffic-using-azure-powershell"></a>使用 Azure PowerShell 限制 Web 流量

此脚本创建具有 Web 应用程序防火墙且使用虚拟机规模集作为后端服务器的应用程序网关。 Web 应用程序防火墙基于 OWASP 规则限制 Web 流量。 在运行脚本后，可以使用应用程序网关的公共 IP 地址测试该网关。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="sample-script"></a>示例脚本

```powershell
# Create a resource group
New-AzResourceGroup -Name myResourceGroupAG -Location chinanorth2

# Create network resources
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location chinanorth2 `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location chinanorth2 `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic

# Create IP configurations and frontend port
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80

# Create the backend pool and settings
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool 
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120

# Create the default listener and rule
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name mydefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings

# Create the application gateway
$sku = New-AzApplicationGatewaySku `
  -Name WAF_Medium `
  -Tier WAF `
  -Capacity 2
$wafConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration `
  -Enabled $true `
  -FirewallMode "Detection"
$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location chinanorth2 `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku `
  -WebApplicationFirewallConfig $wafConfig

# Create a virtual machine scale set
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw
$ipConfig = New-AzVmssIpConfig `
  -Name myVmssIPConfig `
  -SubnetId $vnet.Subnets[1].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id
$vmssConfig = New-AzVmssConfig `
  -Location chinanorth2 `
  -SkuCapacity 2 `
  -SkuName Standard_DS2 `
  -UpgradePolicyMode Automatic
Set-AzVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest
Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword "Azure123456!" `
  -ComputerNamePrefix myvmss
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name myVmssNetConfig `
  -Primary $true `
  -IPConfiguration $ipConfig
New-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmssConfig

# Install IIS
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/davidmu1/samplescripts/master/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss

# Create a storage account
$storageAccount = New-AzStorageAccount `
  -ResourceGroupName myResourceGroupAG `
  -Name myagstore1 `
  -Location chinanorth2 `
  -SkuName "Standard_LRS"

# Configure diagnostics
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$store = Get-AzStorageAccount `
  -ResourceGroupName myResourceGroupAG `
  -Name myagstore1
Set-AzDiagnosticSetting `
  -ResourceId $appgw.Id `
  -StorageAccountId $store.Id `
  -Categories ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog, ApplicationGatewayFirewallLog `
  -Enabled $true `
  -RetentionEnabled $true `
  -RetentionInDays 30

# Get the IP address
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、应用程序网关和所有相关资源。

```powershell
Remove-AzResourceGroup -Name myResourceGroupAG
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
| [New-AzApplicationGatewayWebApplicationFirewallConfiguration](https://docs.microsoft.com/powershell/module/az.network/new-azapplicationgatewaywebapplicationfirewallconfiguration) | 创建 Web 应用程序防火墙配置。 |
| [New-AzApplicationGateway](https://docs.microsoft.com/powershell/module/az.network/new-azapplicationgateway) | 创建应用程序网关。 |
| [Set-AzVmssStorageProfile](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssstorageprofile) | 创建规模集的存储配置文件。 |
| [Set-AzVmssOsProfile](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssosprofile) | 定义规模集的操作系统。 |
| [Add-AzVmssNetworkInterfaceConfiguration](https://docs.microsoft.com/powershell/module/az.compute/add-azvmssnetworkinterfaceconfiguration) | 定义规模集的网络接口。 |
| [New-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | 创建虚拟机规模集。 |
| [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) | 创建存储帐户。 |
| [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) | 配置诊断以记录数据。 |
| [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) | 获取应用程序网关的公共 IP 地址。 |
|[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组及其中包含的所有资源。 | 
## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview)。

可以在 [Azure 应用程序网关文档](../powershell-samples.md)中找到其他应用程序网关 PowerShell 脚本示例。

<!-- Update_Description: code update -->
