---
title: 开发安全的 Azure AD Web 应用程序 | Azure Docs
description: 这个简单的示例应用实施了在 Azure 上进行开发时可以改进应用程序和组织的安全状况的安全最佳做法。
keywords: na
services: security
documentationcenter: na
author: TerryLanfear
manager: alclabo
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2020
ms.author: v-tawe
origin.date: 09/12/2019
ms.openlocfilehash: b346522ac132a21b5ed5f683fe85fca5eac77d58
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96432667"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>开发用于 Azure AD 应用的安全应用
## <a name="overview"></a>概述

此示例是一个简单的 Azure Active Directory，其中包含的 Web 应用链接到用于在 Azure 上开发应用的安全资源。 此应用实施了在 Azure 上开发应用时可以帮助改进应用程序和组织的安全状况的安全最佳做法。

部署脚本会设置基础结构。 运行部署脚本后，你需要在 Azure 门户中进行一些手动配置，以将组件和服务链接在一起。 此示例适用于在 Azure 上具有丰富经验的开发人员，这些开发人员在零售行业中工作，希望使用安全的 Azure 基础结构构建安全的 Azure Active Directory。 


通过开发和部署此应用，你将了解如何执行以下操作： 
- 创建一个 Azure Key Vault 实例，在其中存储和检索机密。
- 部署专用且与前端防火墙访问隔离的 Azure Web 应用。 
- 创建 Azure 应用程序网关实例并为其配置使用 OWASP 10 大规则集的防火墙。 
- 使用 Azure 服务启用传输中数据和静态数据的加密。 
- 设置 Azure Policy 和安全中心来评估合规性。 

开发并部署此应用后，你将设置以下示例 Web 应用以及所述的配置和安全措施。

## <a name="architecture"></a>体系结构
此应用是一个典型的 N 层应用程序，共有三层。 集成了监视和机密管理组件的前端、后端和数据库层如下所示：

![应用体系结构](./media/secure-aad-app/architecture.png)

此解决方案使用以下 Azure 服务。 若要详细了解部署体系结构，请参阅“部署体系结构”部分。 

该体系结构包括以下组件

- [Azure 应用程序网关](/application-gateway/)。 为应用程序体系结构提供网关和防火墙。
- [Application Insights](/azure-monitor/app/app-insights-overview.md)。 在多个平台上提供可扩展的应用程序性能管理 (APM) 服务。
- [Azure Key Vault](/key-vault/)。 存储并加密我们的应用的机密，并管理有关机密的访问策略的创建。
- [Azure Active Directory](/active-directory/fundamentals/active-directory-whatis.md)。提供基于云的标识和访问管理服务、登录和访问资源。
- [Azure 域名系统](/dns/dns-overview.md)。 提供用来承载域的服务。
- [Azure 负载均衡器](/load-balancer/load-balancer-overview.md)。 用来缩放应用程序，并实现服务的高可用性。
- [Azure Web 应用](/app-service/overview.md)。  提供基于 HTTP 的服务来承载 Web 应用程序。
- [Azure 安全中心](/security-center/)。 针对云中的混合工作负荷提供高级威胁防护。
- [Azure Policy](/governance/policy/overview.md)。 用来评估资源是否不符合分配的策略。

## <a name="threat-model"></a>威胁模型
威胁建模是一个过程，它需要先识别对业务和应用程序的潜在安全威胁，然后确保实施适当的缓解计划。

此示例使用了 [Microsoft Threat Modeling Tool](/threat-modeling-tool) 来实施安全的示例应用的威胁建模。 通过绘制组件和数据流的关系图，你可以在开发过程的早期确定问题和威胁。 使用此方法，以后可以节省时间和金钱。

下面是示例应用的威胁模型

![威胁模型](./media/secure-aad-app/threat-model.png)

下面的屏幕截图显示了威胁建模工具生成的一些示例威胁和潜在漏洞。 威胁模型概述了暴露的受攻击面，并提示开发人员考虑如何缓解问题。

![威胁模型输出](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>先决条件
若要启动并运行应用程序，需要安装以下工具：

- 用于修改和查看应用程序代码的代码编辑器。[Visual Studio Code](https://code.visualstudio.com/) 是一个开源选项。
- 在开发计算机上安装 [Azure CLI](/cli/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,)。
- 在系统上安装 [Git](https://git-scm.com/)。 Git 用于在本地克隆源代码。
- [jq](https://stedolan.github.io/jq/)，一个用于以用户友好方式查询 JSON 的 UNIX 工具。

你需要一个 Azure 订阅来部署示例应用的资源。 如果没有 Azure 订阅，则可以[创建试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)来测试示例应用。

安装这些工具后，便可以在 Azure 上部署应用了。

### <a name="implementation-guidance"></a>实施指南
此部署脚本是一个可以分解为四个阶段的脚本。 每个阶段都部署并配置[体系结构关系图](#architecture)中的一个 Azure 资源。

这四个阶段是

- 部署 Azure Key Vault。
- 部署 Azure Web 应用。
- 部署具有 Web 应用程序防火墙的应用程序网关。
- 使用已部署的应用配置 Azure AD。

每个阶段都基于上一个阶段，并使用之前部署的资源的配置。

若要完成实施步骤，请确保你已安装了[先决条件](#prerequisites)下列出的工具。

#### <a name="deploy-azure-key-vault"></a>部署 Azure Key Vault
在本部分中，你将创建并部署用来存储机密和证书的 Azure Key Vault 实例。

完成部署后，会在 Azure 上部署一个 Azure Key Vault 实例。

使用 Powershell 部署 Azure Key Vault
 
1. 为 Azure Key Vault 声明变量。
2. 注册 Azure Key Vault 提供程序。
3. 为实例创建资源组。
4. 在步骤 3 中创建的资源组中创建 Azure Key Vault 实例。

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>以下 Azure AD 用户将具有对 Key Vault 的管理员权限
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>注册 Az 提供程序
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>创建 Azure Key Vault 实例
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'China East2'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>向 Key Vault 添加管理员策略
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>如果你知道用户主体名称，要创建允许用户获取和列出加密密钥、证书和机密的访问策略，请执行以下语句：
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

在使用 Key Vault 访问资源的应用中，最佳做法是使用 Azure 资源的托管标识。 如果 Key Vault 的访问密钥不存储在代码或配置中，则安全状况会更好。

根证书包含在容器中。 获取证书时采取的步骤如下：

1. 从[证书颁发机构](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)下载证书文件。
2. 对证书文件进行解码：

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
此脚本将为应用服务实例创建一个已分配的标识。可以将该标识与 MSI 一起使用，以便与 Azure Key Vault 交互，而不是将机密硬编码到代码或配置中。

在门户中转到 Azure Key Vault 实例，在访问策略选项卡上对已分配的标识进行授权。选择“添加新的访问策略”。 在“选择主体”下，搜索与所创建的应用服务实例的名称相似的应用程序名称。
应当会显示附加到应用程序的一个服务主体。 选择它并保存访问策略页，如以下屏幕截图所示。

因为应用程序只需要检索密钥，所以请在机密选项中选择“获取”权限，从而在减少授予的权限的同时允许访问。

![密钥保管库访问策略](./media/secure-aad-app/kv-access-policy.png)

*创建 Key Vault 访问策略*

保存访问策略，然后在“访问策略”选项卡上保存新更改以更新策略。

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>部署启用了 Web 应用程序防火墙的应用程序网关
在 Web 应用中，建议不要直接向 Internet 上的外部世界公开服务。
负载均衡和防火墙规则针对传入流量提供了更强的安全性和控制，并帮助你管理它。

部署应用程序网关实例

1. 创建资源组来承载应用程序网关。
2. 预配要附加到网关的虚拟网络。
3. 在虚拟网络中为网关创建子网。
4. 预配公共 IP 地址。
5. 预配应用程序网关。
6. 在网关上启用 Web 应用程序防火墙。

```
Connect-AzAccount -EnvironmentName AzureChinaCloud
Select-AzSubscription -SubscriptionId '$SubscriptionId'
New-AzResourceGroup -Name appgw-rg -Location "China East2"

#Create a virtual network and a subnet for the application gateway

#Assign an address range for the subnet to be used for the application gateway.

$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

#Assign an address range to be used for the back-end address pool.

$nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

#Create a virtual network with the subnets defined in the preceding steps.

$vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "China East2" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

#Retrieve the virtual network resource and subnet resources to be used in the steps that follow.

$vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet


#Create a public IP address for the front-end configuration

$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "China East2" -AllocationMethod Dynamic

#Create an application gateway configuration object

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

#Create a front-end IP configuration

$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

#Configure the back-end IP address pool with the IP addresses of the back-end web servers

$pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 10.0.3.11

#Configure the front-end IP port for the public IP endpoint

$fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443

#Configure the certificate for the application gateway. This certificate is used to decrypt and reencrypt the traffic on the application gateway

$passwd = ConvertTo-SecureString  "P@ssword!1" -AsPlainText -Force 
$cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer" -Password $passwd 


#Create the HTTP listener for the application gateway

$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert

#Upload the certificate to be used on the TLS/SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting01" -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the TLS/SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "China East2" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>部署 Azure Web 应用
你可以通过 Azure 应用服务使用 Python、Ruby、C# 和 Java 等语言来构建并承载 Web 应用。 Azure 还支持自定义容器，自定义容器几乎可以允许所有编程语言在 Azure 应用服务平台上运行。

#### <a name="create-an-app-service-plan-in-free-tier"></a>在免费层创建应用服务计划
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>创建 Web 应用
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.chinacloudsites.cn"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsdocsazurecnapp-serviceapp-service-web-tutorial-custom-domainstep-2-create-the-dns-records-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>在继续操作之前，请转到你的自定义域的 Azure 域名系统配置 UI，按照 https://docs.azure.cn/app-service/app-service-web-tutorial-custom-domain#step-2-create-the-dns-records 中的说明为主机名“www”配置 CNAME 记录，并将其指向 Web 应用的默认域名

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>将应用服务计划升级到共享层（自定义域需要的最低层）
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>向 Web 应用添加自定义域名
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.chinacloudsites.cn")

## <a name="guidance-and-recommendations"></a>指导和建议

### <a name="network"></a>网络
完成部署后，你将拥有一个启用了 Web 应用程序防火墙的应用程序网关。

网关实例为 HTTPS 公开了端口 443。 此配置可确保只能通过 HTTPS 在端口 443 上访问我们的应用。

阻止未使用的端口并限制受攻击面暴露是一种安全最佳做法。

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>向应用服务实例添加网络安全组

应用服务实例可以与虚拟网络集成。 此集成允许为它们配置网络安全组策略，以管理应用的传入和传出流量。

1. 若要启用此功能，请在 Azure 应用服务实例边栏选项卡上的“设置”下选择“网络”。 在右侧窗格中的“VNet 集成”下进行配置。

   ![新建虚拟网络集成](./media/secure-web-app/app-vnet-menu.png)

    *为应用服务新建虚拟网络集成*
1. 在下一页上，选择“添加 VNET (预览版)”。

1. 在下一个菜单上，选择在部署中创建的以 `aad-vnet` 开头的虚拟网络。 你可以创建新子网，也可以选择现有子网。
   在本例中，请创建新子网。 将“地址范围”设置为 **10.0.3.0/24**，将子网命名为 **app-subnet**。

   ![应用服务虚拟网络配置](./media/secure-web-app/app-vnet-config.png)

    *应用服务的虚拟网络配置*

现在，你已启用虚拟网络集成，可以向应用中添加网络安全组了。

1. 使用搜索框搜索“网络安全组”。 在结果中选择“网络安全组”。

    ![搜索网络安全组](./media/secure-web-app/nsg-search-menu.png)

    *搜索网络安全组*

2. 在下一菜单上，选择“添加”。 输入 NSG 的 **名称** 以及它应位于其中的 **资源组**。 此 NSG 将应用于应用程序网关的子网。

    ![创建 NSG](./media/secure-web-app/nsg-create-new.png)

    *创建 NSG*

3. 创建 NSG 后，选择它。 在其边栏选项卡中的“设置”下，选择“入站安全规则”。  配置这些设置以允许通过端口 443 进入到应用程序网关中的连接。

   ![配置 NSG](./media/secure-web-app/nsg-gateway-config.png)

   *配置 NSG*

4. 在网关 NSG 的出站规则中，通过创建以服务标记 `AppService` 为目标的规则，添加允许应用服务实例的出站连接的规则

   ![为 NSG 添加出站规则](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *为 NSG 添加出站规则*

    添加另一个出站规则，以允许网关将出站规则发送到虚拟网络。

   ![添加另一个出站规则](./media/secure-web-app/nsg-outbound-vnet.png)

    *添加另一个出站规则*

5. 在 NSG 的子网边栏选项卡中，选择“关联”，选择在部署中创建的虚拟网络，然后选择名为 **gw-subnet** 的网关子网。 NSG 将应用于该子网。

6. 如前面的步骤所述，创建另一个 NSG，这次是为应用服务实例创建。 为其指定名称。 为端口 443 添加入站规则，就像为应用程序网关 NSG 所做的那样。

   如果在应用服务环境实例上部署了应用服务实例（此应用不是这种情况），则可以添加入站规则，通过在应用服务 NSG 的入站安全组中打开端口 454-455 来允许 Azure 服务运行状况探测。 下面是相关配置：

   ![为 Azure 服务运行状况探测添加规则](./media/secure-web-app/nsg-create-healthprobes.png)

    *为 Azure 服务运行状况探测添加规则（仅限应用服务环境）*

若要限制受攻击面，请修改应用服务网络设置，以便仅允许应用程序网关访问应用程序。
若要应用设置，请转到应用服务网络选项卡，选择“IP 限制”选项卡，并创建仅允许应用程序网关的 IP 直接访问服务的允许规则。 可以从网关的概览页检索网关的 IP 地址。 在“IP 地址 CIDR”选项卡上，采用以下格式输入 IP 地址：`<GATEWAY_IP_ADDRESS>/32`。

![仅允许网关](./media/secure-web-app/app-allow-gw-only.png)

*仅允许网关 IP 访问应用服务*

### <a name="azure-domain-name-system"></a>Azure 域名系统 
Azure 域名系统负责将网站或服务名称转换（或解析）为它的 IP 地址。 Azure 域名系统 (https://docs.azure.cn/dns/dns-overview) 是针对域名系统域的一项承载服务，它使用 Azure 基础结构提供名称解析。 通过在 Azure 中托管域，用户可以使用与其他 Azure 服务相同的凭据、API、工具和账单来管理域名系统记录。 Azure 域名系统还支持专用域名系统域。

### <a name="azure-disk-encryption"></a>Azure 磁盘加密
Azure 磁盘加密利用 Windows 的 BitLocker 功能，为数据磁盘提供卷加密。 此解决方案与 Azure Key Vault 集成，可帮助控制和管理磁盘加密密钥。

### <a name="identity-management"></a>身份管理
以下技术在 Azure 环境中提供用于管理持卡人数据访问的功能
- Azure Active Directory 是 Microsoft 提供的多租户基于云的目录和标识管理服务。 此解决方案的所有用户（包括访问 Azure WebApp 的用户）都在 Azure Active Directory 中创建的。
- Azure 基于角色的访问控制使管理员能够定义细粒度的访问权限，以仅授予用户执行作业所需的访问量。 无需向每个用户授予 Azure 资源的不受限权限，管理员可以只允许使用特定的操作来访问持卡人数据。 订阅访问仅限于订阅管理员。
- Azure Active Directory Privileged Identity Management 使客户能够最大限度地减少有权访问持卡人数据等特定信息的用户数量。 管理员可以使用 Azure Active Directory Privileged Identity Management 来发现、限制和监视特权标识及其对资源的访问。 还可以根据需要，使用此功能来实施按需、实时的管理访问。
- Azure Active Directory 标识保护会检测到影响组织标识的潜在漏洞，配置自动化的措施来应对所检测到的与组织标识相关的可疑操作，调查可疑的事件以采取相应的措施予以解决。
### <a name="secrets-management"></a>机密管理
此解决方案使用 Azure Key Vault 管理密钥和机密。 Azure 密钥保管库可帮助保护云应用程序和服务使用的加密密钥和机密。 以下 Azure Key Vault 功能可帮助客户保护和访问此类数据
   - 根据需要配置高级访问权限策略。
   - 使用对密钥和机密所需的最低权限来定义 Key Vault 访问策略。
   - Key Vault 中的所有密钥和机密都有过期日期。
   - Key Vault 中的所有密钥受专用硬件安全模块的保护。 密钥类型是硬件安全模块 (HSM) 保护的 2048 位 RSA 密钥。
   - 通过 Key Vault，可以使用受硬件安全模块 (HSM) 保护的密钥，来加密密钥和机密（例如身份验证密钥、存储帐户密钥、数据加密密钥、.PFX 文件和密码）。 
   - 使用基于角色的访问控制 (RBAC) 向特定范围的用户、组和应用程序分配权限。     
   - 使用 Key Vault 和自动续订来管理 TLS 证书。 
   - Key Vault 的诊断日志已启用，其保留期至少为 365 天。
   - 对密钥进行允许的加密操作时，仅限必需的操作。
### <a name="azure-security-center"></a>Azure 安全中心
借助 Azure 安全中心，客户可在工作负载中集中应用和管理安全策略、限制威胁暴露，以及检测和应对攻击。 此外， 
   - Azure 安全中心还会访问 Azure 服务的现有配置，以提供配置与服务建议来帮助改善安全状况和保护数据。
   - Azure 安全中心使用各种检测功能，提醒客户针对其环境的潜在攻击。 这些警报包含有关触发警报的内容、目标资源以及攻击源的重要信息。 Azure 安全中心有一组预定义的安全警报，这些警报在出现威胁或可疑活动时触发。 客户可以使用 Azure 安全中心的自定义警报规则，根据从环境中收集到的数据定义新的安全警报。
   - Azure 安全中心提供区分优先级的安全警报和事件，让客户更轻松地发现和解决潜在安全问题。 针对检测到的每种威胁生成威胁情报报告，帮助事件响应团队调查和解决威胁。
### <a name="azure-application-gateway"></a>Azure 应用程序网关 
   体系结构使用配置了 Web 应用程序防火墙并启用了 OWASP 规则集的应用程序网关，来降低安全漏洞风险。 其他功能包括
   - 端到端 TLS。
   - 禁用 TLS v1.0 和 v1.1。
   - 启用 TLSv1.2。
   - Web 应用程序防火墙（预防模式）。
   - 采用 OWASP 3.0 规则集的预防模式。
   - 启用诊断日志记录。
   - 自定义运行状况探测。
   - Azure 安全中心和 Azure 顾问提供了额外的保护和通知。 Azure 安全中心还提供信誉系统。
### <a name="logging-and-auditing"></a>日志记录和审核
Azure 服务广泛记录系统和用户活动以及系统运行状况：
   - 活动日志：[活动日志](https://docs.azure.cn/monitoring-and-diagnostics/monitoring-overview-activity-logs)提供对订阅中资源执行的操作的深入信息。 活动日志可帮助确定操作的发起方、发生的时间和状态。
   - 诊断日志：[诊断日志](https://docs.azure.cn/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)包括每个资源发出的所有日志。 这些日志包括 Windows 事件系统日志、Azure 存储日志、Key Vault 审核日志以及应用程序网关访问和防火墙日志。 所有诊断日志都将写入到集中式加密 Azure 存储帐户以进行存档。 保留期是允许用户配置的，最长为 730 天，具体取决于组织的保留期要求。
### <a name="azure-monitor-logs"></a>Azure Monitor 日志
   这些日志将整合到 [Azure Monitor 日志](https://www.azure.cn/pricing/details/monitor/)中进行处理、存储以及在仪表板上报告。 收集后，数据在 Log Analytics 工作区内按数据类型整理到不同的表中，这样即可不考虑最初来源而集中分析所有数据。 此外，Azure 安全中心与 Azure Monitor 日志集成，使客户能够使用 Kusto 查询来访问其安全事件数据，并将这些数据与其他服务中的数据合并。

   此体系结构中包括了以下 Azure [监视解决方案](https://docs.azure.cn/azure-monitor/insights/solutions-inventory)

   - [Active Directory 评估](/azure-monitor/insights/ad-assessment)：Active Directory 运行状况检查解决方案按固定时间间隔评估服务器环境的风险和运行状况，并且提供特定于部署的服务器基础结构的优先建议列表。
   - [代理运行状况](/azure-monitor/insights/solution-agenthealth)：代理运行状况解决方案报告已部署代理的数量及其地理分布，以及无响应的代理数量和提交操作数据的代理数量。
   - [Activity Log Analytics](https://docs.azure.cn/log-analytics/log-analytics-activity)：Activity Log Analytics 解决方案可帮助分析客户的所有 Azure 订阅的 Azure 活动日志。
### <a name="azure-monitor"></a>Azure Monitor
   [Azure Monitor](https://docs.azure.cn/monitoring-and-diagnostics/) 通过使组织能够审核、创建警报和存档数据（包括在用户的 Azure 资源中跟踪 API 调用），帮助用户跟踪性能、维护安全性和确定趋势。
### <a name="application-insights"></a>Application Insights 
   [Application Insights](https://docs.azure.cn/application-insights/app-insights-overview) 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理服务。 Application Insights 可检测性能异常，客户可以使用它来监视实时 Web 应用程序。 它包含强大的分析工具来帮助客户诊断问题，了解用户在应用中实际执行了哪些操作。 它旨在帮助客户持续提高性能和可用性。

### <a name="azure-key-vault"></a>Azure Key Vault
   为组织创建一个保管库，以便在其中存储密钥，使如下所示的操作任务始终能够追责

   - Key Vault 中存储的数据包括   
   - Application Insights 密钥
   - 数据存储访问密钥
   - 连接字符串
   - 数据表名称
   - 用户凭据
   - 根据需要配置高级访问权限策略
   - 使用对密钥和机密所需的最低权限来定义 Key Vault 访问策略
   - Key Vault 中的所有密钥和机密都有过期日期
   - Key Vault 中的所有密钥都由硬件安全模块 (HSM) 提供保护。[密钥类型是硬件安全模块 (HSM) 保护的       
     2048 位 RSA 密钥]
   - 使用基于角色的访问控制 (RBAC) 向所有用户/标识授予了最低必需权限
   - 应用程序共享 Key Vault 的前提是彼此信任且需要在运行时访问相同的机密
   - Key Vault 的诊断日志已启用，其保留期至少为 365 天。
   - 对密钥进行允许的加密操作时，仅限必需的操作

### <a name="vpn-and-expressroute"></a>VPN 和 ExpressRoute
   需要配置安全 VPN 隧道或 [ExpressRoute](https://docs.azure.cn/expressroute/expressroute-introduction)，方法是：安全地建立与作为此 PaaS Web 应用程序参考体系结构的一部分部署的资源的连接。 通过适当设置 VPN 或 ExpressRoute，客户可以为传输中的数据添加一层保护。

   在 Azure 中实施安全 VPN 隧道，可在本地网络与 Azure 虚拟网络之间创建虚拟专用连接。 此连接通过 Internet 进行，可让客户在其网络与 Azure 之间的加密链路内通过“隧道”安全地传输信息。 站点到站点 VPN 是安全且成熟的技术，各种规模的企业已部署该技术数十年。 此选项使用 IPsec 隧道模式作为加密机制。

   由于 VPN 隧道中的流量会通过站点到站点 VPN 在 Internet 上遍历，Microsoft 提供了另一个更安全的连接选项。 Azure ExpressRoute 是 Azure 与本地位置或 Exchange 托管提供商之间专用的 WAN 链接。 ExpressRoute 连接并不绕过 Internet，并且与通过 Internet 的典型连接相比，这些连接可靠性更高、速度更快、延迟时间更短且安全性更高。 此外，由于使用的是客户电信提供商的直接连接，数据不会通过 Internet 遍历，因此不会在 Internet 上公开。

<!-- not available-->

#### <a name="implement-azure-active-directory-oidc"></a>实现 Azure Active Directory OIDC

1. 若要克隆源代码存储库，请使用此 Git 命令

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>更新重定向 URL
1.  导航回 Azure 门户。 在左侧导航窗格中，选择“Azure Active Directory”服务，然后选择“应用注册”。
2.  在出现的屏幕中，选择 WebApp-OpenIDConnect-DotNet-code-v2 应用程序。
3.  在“身份验证”选项卡的“重定向 URI”部分的组合框中选择“Web”并添加以下重定向 URI。
    `https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.chinacloudsites.cn`
    `https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.chinacloudsites.cn/signin-oidc`
    在“高级设置”部分中，将“注销 URL”设置为 `https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.chinacloudsites.cn/signout-oidc`
4.  在“品牌”选项卡中，将“主页 URL”更新为你的应用服务的地址，例如 `https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.chinacloudsites.cn`。
        保存配置。
5.  如果你的应用程序调用了某个 Web API，请确保在项目的 appsettings.json 中应用必要的更改，使其调用已发布的 API URL 而不是调用 localhost。
发布示例
    1.  在“应用服务”的“概览”选项卡中，单击“获取发布配置文件”链接下载该配置文件并保存。 还可以使用其他部署机制，如源代码管理。
    2.  切换到 Visual Studio 并转到 WebApp-OpenIDConnect-DotNet-code-v2 项目。 在解决方案资源管理器中，右键单击该项目并选择“发布”。 在底部栏上单击“导入配置文件”，然后导入先前下载的发布配置文件。
    3.  单击“配置”，然后在“连接”选项卡中更新“目标 URL”，使其成为主页 URL 中的一个 https，例如 `https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.chinacloudsites.cn`。 单击“下一步”。
    4.  在“设置”选项卡上，确保未选中“启用组织身份验证”。 单击“保存”。 在主屏幕上单击“发布”。
    5.  Visual Studio 将发布项目，并自动打开浏览器以访问该项目的 URL。 如果看到该项目的默认网页，则发布成功。
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>为 Azure Active Directory 实施多重身份验证
   管理员需要确保门户中的订阅帐户受到保护。 订阅容易受到攻击，因为它管理你创建的资源。 若要保护订阅，请在订阅的“Azure Active Directory”选项卡上启用多重身份验证。

   Azure AD 根据应用于符合特定条件的用户或用户组的策略来运行。
Azure 会创建一个默认策略，指定管理员需要通过双重身份验证才能登录到门户。
启用此策略后，系统可能会提示你注销并重新登录到 Azure 门户。

为管理员登录启用 MFA

   1. 在 Azure 门户中，转到“Azure Active Directory”选项卡
   2. 在“安全性”目录下，选择“条件访问”。 你将看到以下屏幕

       ![条件访问 - 策略](./media/secure-aad-app/ad-mfa-conditional-add.png)

如果无法创建新策略

   1. 转到“MFA”选项卡。
   2. 选择一个 Azure AD Premium“免费试用版”链接来订阅免费试用版。

   ![Azure AD Premium 免费试用版](./media/secure-aad-app/ad-trial-premium.png)

返回到条件访问屏幕。

   1. 选择“新建策略”选项卡。
   2. 输入策略名称。
   3. 选择要为其启用 MFA 的用户或组。
   4. 在“访问控制”下，选择“授权”选项卡，然后选择“要求多重身份验证”（并根据需要选择其他设置）。

   ![要求 MFA](./media/secure-aad-app/ad-mfa-conditional-add.png)

   你可以通过选择屏幕顶部的复选框来启用此策略，也可以在“条件访问”选项卡上执行此操作。当启用此策略后，用户需要进行 MFA 才能登录到门户。

   有一个基线策略要求所有 Azure 管理员进行 MFA。 可以在门户中立即启用此基线策略。 启用此策略可能会导致当前会话失效，并强制你再次登录。

   如果未启用此基线策略
   1.   选择“要求管理员进行 MFA”。
   2.   选择“立即使用策略”。

   ![选择“立即使用策略”](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>使用 Azure Sentinel 监视应用和资源

   随着应用程序的发展，很难聚合从资源接收到的所有安全信号和指标，并使其以面向操作的方式发挥作用。

   Azure Sentinel 设计用来收集数据、检测可能的威胁类型，并洞察安全事件。
在等待手动干预的同时，Azure Sentinel 可以依赖预编写的 playbook 来启动警报和事件管理流程。

   示例应用包括了 Azure Sentinel 可以监视的多个资源。
若要设置 Azure Sentinel，首先需要创建一个 Log Analytics 工作区，用于存储从各种资源收集的所有数据。

创建此工作区

   1. 在 Azure 门户中的搜索框中搜索“Log Analytics”。 选择“Log Analytics 工作区”。

   ![搜索 Log Analytics 工作区](./media/secure-aad-app/sentinel-log-analytics.png)

   搜索 Log Analytics 工作区

   2. 在下一页上，选择“添加”，然后提供工作区的名称、资源组和位置。
   ![创建 Log Analytics 工作区](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *创建 Log Analytics 工作区*

   3. 使用搜索框来搜索“Azure Sentinel”。

   ![搜索 Azure Sentinel](./media/secure-aad-app/sentinel-add.png)

   搜索 Azure Sentinel

   4. 选择“添加”，然后选择你之前创建的 Log Analytics 工作区。

   ![添加 Log Analytics 工作区](./media/secure-aad-app/sentinel-workspace-add.png)

   添加 Log Analytics 工作区

   5. 在“Azure Sentinel - 数据连接器”页上的“配置”下选择“数据连接器”。 你将看到可以链接到 Log Analytics 存储实例以在 Azure Sentinel 中进行分析的一系列 Azure 服务。

   ![Log Analytics 数据连接器](./media/secure-aad-app/sentinel-connectors.png)

      向 Azure Sentinel 添加数据连接器

   例如，若要连接应用程序网关，请执行以下步骤：

   1. 打开 Azure 应用程序网关实例边栏选项卡。
   2. 在“监视”下，选择“诊断设置” 。
   3. 选择“添加诊断设置”。

   ![添加应用程序网关诊断](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   添加应用程序网关诊断

   4. 在“诊断设置”页上，选择你创建的 Log Analytics 工作区，然后选择你要收集并发送到 Azure Sentinel 的所有指标。 选择“保存” 。

   ![Azure Sentinel 连接器设置](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>成本注意事项
   如果还没有 Azure 帐户，可以创建一个免费帐户。 转到[试用版帐户页面](https://www.microsoft.com/china/azure/index.html?fromtype=cn)以开始创建，了解可以使用免费 Azure 帐户执行哪些操作，并了解哪些产品可以免费使用 12 个月。

   若要为示例应用中的资源部署安全功能，你需要付费购买一些高级功能。 随着应用不断扩展，你需要升级 Azure 提供的免费层和试用版来满足应用程序需求，你的成本可能会增加。 使用 Azure [定价计算器](https://www.azure.cn/pricing/calculator/)来估算成本。

## <a name="next-steps"></a>后续步骤
   以下文章可帮助你设计、开发和部署安全的应用程序。

- [设计](secure-design.md)
- [开发](secure-develop.md)
- [部署](secure-deploy.md)
