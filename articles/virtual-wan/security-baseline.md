---
title: 适用于虚拟 WAN 的 Azure 安全基线
description: 虚拟 WAN 安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
ms.service: virtual-wan
ms.topic: conceptual
origin.date: 11/24/2020
author: rockboyfor
ms.date: 12/14/2020
ms.testscope: no
ms.testdate: 12/14/2020
ms.author: v-yeche
ms.custom: subject-security-benchmark
ms.openlocfilehash: 27cc7c7b2a500ce44c53de4780497735fb1b5762
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105585"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>适用于虚拟 WAN 的 Azure 安全基线

此安全基线将 [Azure 安全基准版本 2.0](../security/benchmarks/overview.md) 中的指南应用于 Azure 虚拟 WAN。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容按“安全控制”分组，这些控制由适用于虚拟 WAN 的 Azure 安全基准和相关指南定义。 排除了不适用于虚拟 WAN 的控制。

若要查看虚拟 WAN 到 Azure 安全基准的完整映射，请参阅[完整的虚拟 WAN 安全基准映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](https://docs.azure.cn/security/benchmarks/security-controls-v2-network-security)。

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1：实现内部流量的安全性

**指导**：Azure 虚拟 WAN 提供自定义路由功能，并为 ExpressRoute 流量提供加密。 所有路由管理功能均由虚拟中心路由器提供，该路由器还启用了虚拟网络之间的传输连接。 使用虚拟 WAN 加密 ExpressRoute 流量，可通过 ExpressRoute 在本地网络和 Azure 虚拟网络之间提供加密的传输，而无需通过公共 Internet 或使用公共 IP 地址。 

- [ExpressRoute 流量加密](virtual-wan-about.md#encryption)

- [在虚拟 WAN 中使用专用链接](howto-private-link.md)

- [自定义路由方案](scenario-any-to-any.md)

- [自定义路由表文档](how-to-virtual-hub-routing.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ns-2-connect-private-networks-together"></a>NS-2：将专用网络连接在一起 

**指导**：Azure ExpressRoute 提供与 Azure 虚拟 WAN 的专用连接。 由于 ExpressRoute 连接并不通过公共 Internet，因此与典型的 Internet 连接相比，ExpressRoute 可靠性更高、速度更快且延迟时间更短。 还可以通过站点到站点 (S2S) VPN 或点到站点 (P2S) VPN，使用虚拟专用网络连接到 Azure 中国。

- [虚拟 WAN 中的 ExpressRoute](virtual-wan-expressroute-portal.md)

- [站点到站点 VPN 概述](virtual-wan-site-to-site-portal.md)

- [点到站点 VPN 概述](virtual-wan-point-to-site-portal.md)

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4：保护应用程序和服务不受外部网络攻击

**指导**：虚拟 WAN 不向外部网络公开任何终结点，因此需要通过常规网络保护来保护它们。 可以使用虚拟网络保护服务来保护辐射型虚拟网络（连接到虚拟中心的任何虚拟网络）中的资源。 

使用 Azure 防火墙保护应用程序和服务免受来自 Internet 和其他外部位置的潜在恶意流量的侵害。 

选择 Azure 提供的 DDoS 防护，保护 Azure 虚拟网络上的资产免受攻击。 使用 Azure 安全中心来检测网络相关资源的配置错误风险。

- [Azure 防火墙文档](https://docs.azure.cn/firewall)

<!--Not Available on - [Manage Azure DDoS Protection Standard using the Azure portal](https://docs.azure.cn/virtual-network/manage-ddos-protection)-->

- [Azure 安全中心建议](../security-center/recommendations-reference.md#recs-network)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5：部署入侵检测/入侵防护系统 (IDS/IPS)

**指导**：虚拟 WAN 是一项 Azure 托管服务。 它不提供本机入侵检测或入侵防护功能。 不过，我们通过 Azure 防火墙向虚拟 WAN 提供了安全功能，以实现统一的策略控制点。 可以创建 Azure 防火墙策略，并将该策略链接到虚拟 WAN 中心，以允许现有虚拟 WAN 中心充当安全虚拟中心，并部署所需的 Azure 防火墙资源。

- [在虚拟 WAN 中心配置 Azure 防火墙](howto-firewall.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ns-6-simplify-network-security-rules"></a>NS-6：简化网络安全规则

**指导**：在网络安全组或 Azure 防火墙中利用虚拟网络服务标记来定义网络访问控制，可简化网络安全规则。 创建安全规则时，可以使用服务标记代替特定 IP 地址。 通过在规则的“源”或“目标”字段中指定服务标记名称，可允许或拒绝相应服务的流量。 Azure 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

- [了解并使用服务标记](../virtual-network/service-tags-overview.md)

- [了解并使用应用程序安全组](https://docs.azure.cn/virtual-network/security-overview#application-security-groups)

- [Azure 防火墙文档](https://docs.azure.cn/firewall/)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7：安全域名服务 (DNS)

**指导**：通过 Azure 防火墙向虚拟 WAN 提供安全 DNS 功能。 配置 Azure 防火墙，使其充当 DNS 代理，作为从客户端虚拟机到 DNS 服务器的 DNS 请求的中介。 对于自定义 DNS 服务器配置，请启用 DNS 代理以避免 DNS 解析不匹配，并在网络规则中启用完全限定的域名筛选。 

- [Azure 防火墙文档](https://docs.azure.cn/firewall/)

- [Azure 防火墙 DNS 设置](https://docs.azure.cn/firewall/dns-settings)

- [使用 Azure 防火墙作为专用链接的 DNS 转发器](https://github.com/adstuart/azure-privatelink-dns-azurefirewall)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="identity-management"></a>标识管理

有关详细信息，请参阅 [Azure 安全基准：标识管理](https://docs.azure.cn/security/benchmarks/security-controls-v2-identity-management)。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：将 Azure Active Directory 标准化为中央标识和身份验证系统

**指导**：Azure Active Directory (Azure AD) 是 Azure 服务的默认标识和访问管理服务。 包括虚拟 WAN。 使 Azure AD 标准化，以便控制组织在以下资源中的标识和访问管理：

- Azure 云资源，例如 Azure 门户、Azure 存储、Azure 虚拟机（Linux 和 Windows）、Azure Key Vault、平台即服务 (PaaS) 和软件即服务 (SaaS) 应用程序。
- 你的组织的资源，例如 Azure 上的应用程序，或公司网络资源

保护 Azure AD 是组织云安全实践中的高优先级工作。 利用安全中心的安全分数功能评估你的标识和安全状况，以衡量你的配置与 Azure 的最佳做法建议之间的距离。 根据需要，实现 Azure 的最佳做法建议以改进安全状况。

Azure AD 还支持外部标识，这让没有 Microsoft 帐户的用户可以使用其外部标识登录到其应用程序和资源。 

请参阅引用的链接，了解有关在点到站点 VPN 方案中使用 Azure AD 的信息。

- [为 P2S OpenVPN 协议连接创建 Azure Active Directory (AD) 租户](openvpn-azure-ad-tenant-multi-app.md)

- [为用户 VPN 配置 Azure Active Directory 身份验证](virtual-wan-point-to-site-azure-ad.md)

- [Azure Active Directory 中的租赁](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：对所有基于 Azure Active Directory 的访问使用强身份验证控制

**指导**：目前，通过与虚拟 WAN 点到站点 VPN 集成提供 Azure Active Directory (Azure AD) 身份验证。

Azure Active Directory (Azure AD) 是 Azure 服务的默认标识和访问管理服务。 Azure AD 支持通过多重身份验证和强无密码方法进行强身份验证控制。

Azure AD 建议通过以下方案实现强身份验证控件：

- 多重身份验证 - 启用 Azure AD 多重身份验证，并遵循 Azure 安全中心的“标识和访问管理”建议，以执行安全方面的最佳做法。 根据登录条件和风险因素，对所有用户、选定用户或以用户为单位强制执行多重身份验证

- 无密码身份验证 - 提供三个无密码身份验证选项。 包括 Windows Hello for Business、Azure Authenticator 应用和本地身份验证方法（例如智能卡）

请确保对管理员和特权用户使用最高级别的强身份验证方法，然后向其他用户推出强身份验证策略。

- [如何在 P2S VPN 中为虚拟 WAN 启用 MFA](openvpn-azure-ad-mfa.md)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：基于条件限制 Azure 资源访问

**指导**：为使用 Azure AD 身份验证的 VPN 用户（点到站点）启用 Azure Active Directory (Azure AD) 多重身份验证。 为每个用户配置多重身份验证，或通过条件访问利用多重身份验证。 使用条件访问可对提升第二个因素的方式进行更细粒度的控制。 它可以允许仅将多重身份验证分配给 VPN，并排除绑定到 Azure AD 租户的其他应用程序。 

请注意，Azure AD 身份验证仅适用于使用 OpenVPN 协议的网关以及运行 Windows 的客户端。

- [什么是条件访问](../active-directory/conditional-access/overview.md)

- [为用户 VPN 配置 Azure Active Directory 身份验证](virtual-wan-point-to-site-azure-ad.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7：消除意外的凭据透露

**指导**：虚拟 WAN 中的站点到站点 VPN 使用提前共享的密钥 (PSK)，客户可在其 Azure Key Vault 中发现、创建和管理这类秘钥。 实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

对于 GitHub，你可以使用原生的机密扫描功能来识别代码中的凭据或其他形式的机密。

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [GitHub 机密扫描](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="privileged-access"></a>特权访问

有关详细信息，请参阅 [Azure 安全基准：特权访问](https://docs.azure.cn/security/benchmarks/security-controls-v2-privileged-access)。

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制对关键业务型系统的管理访问权限

**指导**：Azure 虚拟 WAN 使用 Azure 基于角色的访问控制 (Azure RBAC)，限制向哪些帐户授予对其所属的订阅和管理组的特权访问权限，从而隔离对业务关键系统的访问。

还限制了对业务关键型资产具有管理访问权限的管理、标识和安全系统的访问，这些资产包括在业务关键型系统上安装了代理的 Active Directory 域控制器、安全工具和系统管理工具。 入侵这些管理和安全系统的攻击者可以立即将它们用作损害业务关键型资产的武器。

所有类型的访问控制都应符合企业分段策略，确保访问控制保持一致。

<!--Not Available on - [Azure Components and Reference model](https://docs.azure.cn/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) -->

- [访问管理组](../governance/management-groups/overview.md#management-group-access) 

<!--Not Available on - [Azure subscription administrators](../cost-management-billing/manage/add-change-subscription-administrator.md)-->

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](https://docs.azure.cn/security/benchmarks/security-controls-v2-data-protection)。

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4：加密传输中的敏感信息

**指导**：将点到站点 VPN、站点到站点 VPN 和加密 Express Route 用于虚拟 WAN，以满足你的连接要求。 VPN 加密可保护传输中的数据免受“带外”攻击（例如流量捕获），确保攻击者无法读取或修改数据。 

- [点到站点 VPN](virtual-wan-point-to-site-portal.md)

- [站点到站点 VPN](virtual-wan-site-to-site-portal.md)

- [加密 ExpressRoute](vpn-over-expressroute.md)

**Azure 安全中心监视**：是

**责任**：共享

## <a name="asset-management"></a>资产管理

有关详细信息，请参阅 [Azure 安全基准：资产管理](https://docs.azure.cn/security/benchmarks/security-controls-v2-asset-management)。

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1：确保安全团队可以了解与资产相关的风险

**指南**：确保在 Azure 租户和订阅中向安全团队授予了安全读取者权限，以便他们可以使用 Azure 安全中心监视安全风险。 

根据安全团队责任划分方式的不同，监视安全风险可能是中心安全团队或本地团队的责任。 这样，安全见解和风险必须始终在组织内集中聚合。 

安全读取者权限可以广泛应用于整个租户（根管理组），也可以限制到管理组或特定订阅。 

注意：若要了解工作负载和服务，可能需要更多权限。 

- [安全读取者角色概述](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理组概述](../governance/management-groups/overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2：确保安全团队有权访问资产清单和元数据

**指导**：将标记应用到 Azure 资源、资源组和订阅，以便有条理地将它们组织成分类。 每个标记均由名称和值对组成。 例如，可以对生产中的所有资源应用名称“Environment”和值“Production”。 Azure 虚拟 WAN 还支持基于 Azure 资源管理器的资源部署，可以通过它们导出资产模板。 

<!--Not Available on - [Resource naming and tagging decision guide](https://docs.azure.cn/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure-resource-manager/management/toc.json)-->    

- [Azure 安全中心资产库存管理](../security-center/asset-inventory.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="am-3-use-only-approved-azure-services"></a>AM-3：仅使用已批准的 Azure 服务

**指导**：使用 Azure Monitor 来创建规则，以便在检测到未经批准的服务时触发警报。 虚拟 WAN 整合了多种网络、安全和路由功能，提供单一操作界面。 虚拟 WAN VPN 网关、ExpressRoute 网关和 Azure 防火墙都具有通过 Azure Monitor 提供的日志记录和指标。 

- [虚拟 WAN 日志和指标](logs-metrics.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5：限制用户与 Azure 资源管理器进行交互的能力

**指导**：通过为“Azure 管理”应用配置“阻止访问”，使用 Azure 条件访问来限制用户与 Azure 资源管理器交互的能力。

- [如何配置条件访问以阻止访问 Azure 资源管理器](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="logging-and-threat-detection"></a>日志记录和威胁检测

<!--Not Available on *For more information, see the [Azure Security Benchmark: Logging and Threat Detection](https://docs.azure.cn/security/benchmarks/security-controls-v2-logging-threat-protection)-->

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1：为 Azure 资源启用威胁检测

**指导**：通过虚拟 WAN 实现的点到站点 VPN 与 Azure Active Directory (Azure AD) 集成。 Azure AD 提供以下用户日志，可在 Azure AD 报表中进行查看，也可将这些日志与 Azure Monitor、Azure Sentinel、SIEM 或监视工具集成，以用于更复杂的威胁监视和分析用例。 这些是：

- 登录 - 登录报告提供有关托管应用程序使用情况和用户登录活动的信息。
- 审核日志 - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中任何资源的更改，例如添加或删除用户、应用、组、角色和策略。
- 风险登录 - 风险登录指示可能有用户帐户合法拥有者以外的人进行了登录尝试。
- 已标记为存在风险的用户 - 风险用户是指可能已泄露的用户帐户。

使用 Azure 安全中心，可针对某些可疑活动发出警报，例如身份验证尝试失败次数过多（包括使用订阅中已弃用的帐户造成的失败）。 除了基本的安全机制监视，安全中心的威胁防护模块还可从各个 Azure 计算资源（虚拟机、容器、应用服务）、数据资源（SQL DB 和存储）和 Azure 服务层收集更深入的安全警报。 通过此功能，可查看各个资源内的帐户异常情况。

- [Azure Active Directory 中的“审核活动”报表](../active-directory/reports-monitoring/concept-audit-logs.md) 

<!--Not Available on- [Enable Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)-->

- [在虚拟 WAN 中心配置 Azure 防火墙](howto-firewall.md)

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2：启用 Azure 标识和访问管理的威胁检测

**指导**：通过虚拟 WAN 实现的点到站点 VPN 与 Azure Active Directory (Azure AD) 集成。 Azure AD 提供以下用户日志，可在 Azure AD 报表中进行查看，也可将这些日志与 Azure Monitor、Azure Sentinel、SIEM 或监视工具集成，以用于更复杂的威胁监视和分析用例。 这些是：

- 登录 - 登录报告提供有关托管应用程序使用情况和用户登录活动的信息。
- 审核日志 - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中任何资源的更改，例如添加或删除用户、应用、组、角色和策略。
- 风险登录 - 风险登录指示可能有用户帐户合法拥有者以外的人进行了登录尝试。
- 已标记为存在风险的用户 - 风险用户是指可能已泄露的用户帐户。

使用 Azure 安全中心，可针对某些可疑活动发出警报，例如身份验证尝试失败次数过多（包括使用订阅中已弃用的帐户造成的失败）。 除了基本的安全机制监视，安全中心的威胁防护模块还可从各个 Azure 计算资源（虚拟机、容器、应用服务）、数据资源（SQL DB 和存储）和 Azure 服务层收集更深入的安全警报。 通过此功能，可查看各个资源内的帐户异常情况。

- [Azure Active Directory 中的“审核活动”报表](../active-directory/reports-monitoring/concept-audit-logs.md) 

<!--Not Available on- [Enable Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)-->

- [在虚拟 WAN 中心配置 Azure 防火墙](howto-firewall.md)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3：为 Azure 网络活动启用日志记录

**指导**：使用 Azure Monitor 监视 Azure 虚拟 WAN。 虚拟 WAN 整合了多种网络、安全和路由功能，提供单一操作界面。 虚拟 WAN VPN 网关、ExpressRoute 网关和 Azure 防火墙都具有通过 Azure Monitor 提供的日志记录和指标。 默认情况下会收集活动日志条目，可在 Azure 门户中查看这些条目。 可以使用 Azure 活动日志（以前称为操作日志和审核日志）查看提交到 Azure 订阅的所有操作。

还为虚拟 WAN 提供了各种诊断日志，可使用 Azure 门户为虚拟 WAN 资源配置这些日志。  你可以选择发送到 Log Analytics、流式传输到事件中心，或者直接存档到某个存储帐户。 

- [虚拟 WAN 日志和指标](logs-metrics.md)

- [Azure 防火墙日志和指标](https://docs.azure.cn/firewall/logs-and-metrics)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：为 Azure 资源启用日志记录

**指导**：自动启用了 Azure 活动日志，其中包含针对 Azure 虚拟 WAN 资源的所有写入操作（PUT、POST、DELETE），但读取操作 (GET) 除外。 活动日志可用于在进行故障排除时查找错误，或监视组织中的用户如何对资源进行修改。

为虚拟 WAN 启用 Azure 资源日志。 可以使用 Azure 安全中心和 Azure Policy 来启用资源日志和日志数据收集。 这些日志可能对日后调查安全事件和执行取证演练至关重要。

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md) 

- [了解 Azure 中的日志记录和不同的日志类型](../azure-monitor/platform/platform-logs-overview.md) 

- [了解 Azure 安全中心数据收集](../security-center/security-center-enable-data-collection.md)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：集中管理和分析安全日志

**指导**：使用 Azure Monitor 为虚拟 WAN 启用安全日志记录。 虚拟 WAN 整合了多种网络、安全和路由功能，提供单一操作界面。 虚拟 WAN VPN 网关、ExpressRoute 网关和 Azure 防火墙都具有通过 Azure Monitor 提供的日志记录和指标。 默认情况下会收集活动日志条目，可在 Azure 门户中查看这些条目。 可以使用 Azure 活动日志（以前称为操作日志和审核日志）查看提交到 Azure 订阅的所有操作。 

还为虚拟 WAN 提供了各种诊断日志，可使用 Azure 门户为虚拟 WAN 资源配置这些日志。 发送到 Log Analytics，流式传输到事件中心，或者直接存档到某个存储帐户。 另外，请启用 Azure Sentinel 或第三方安全信息和事件管理解决方案并将数据载入其中。 

- [虚拟 WAN 日志和指标](logs-metrics.md)

- [Azure 防火墙日志和指标](https://docs.azure.cn/firewall/logs-and-metrics)

Azure 虚拟 WAN 安全性通过 Azure 防火墙实现。 

- [Azure 防火墙文档](https://docs.azure.cn/firewall/overview)

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="lt-6-configure-log-storage-retention"></a>LT-6：配置日志存储保留期

**指导**：根据合规性、法规和业务要求配置日志保留。 在 Azure Monitor 中，可根据组织的合规性规则设置 Log Analytics 工作区保持期。 将 Azure 存储、Data Lake 或 Log Analytics 工作区帐户用于长期存储和存档存储。

- [更改 Log Analytics 中的数据保留期](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [如何为 Azure 存储帐户日志配置保留策略](../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Azure 安全中心警报和建议导出](../security-center/continuous-export.md)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](https://docs.azure.cn/security/benchmarks/security-controls-v2-incident-response)。

<!--Not Available on - [Implement security across the enterprise environment](https://docs.azure.cn/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)-->

<!--Not Available on - [Incident response reference guide](https://docs.microsoft.com/microsoft-365/downloads/IR-Reference-Guide.pdf)-->

### <a name="ir-2-preparation---setup-incident-notification"></a>IR-2：准备 - 设置事件通知

**指导**：在 Azure 安全中心中设置安全事件联系人信息。 如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的一方访问了你的数据，Azure 将使用此联系信息来与你联系。 还可以选择基于事件响应需求在不同的 Azure 服务中自定义事件警报和通知。 

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="ir-3-detection-and-analysis---create-incidents-based-on-high-quality-alerts"></a>IR-3：检测和分析 - 基于高质量警报创建事件

**指南**：确保你有创建高质量警报和衡量警报质量的流程。 这样，你就可以从过去的事件中吸取经验，并为分析人员确定警报的优先级，这样他们就不会浪费时间来处理误报。 

可以基于从过去的事件中吸取的经验、经过验证的社区来源以及各种工具来生成高质量警报，这些工具旨在通过融合和关联各种信号源来生成和清除警报。 

Azure 安全中心可跨许多 Azure 资产提供高质量的警报。 可以使用 ASC 数据连接器将警报流式传输到 Azure Sentinel。 借助 Azure Sentinel，可创建高级警报规则来自动生成事件以进行调查。 

使用导出功能导出 Azure 安全中心警报和建议，以帮助识别 Azure 资源的风险。 手动导出或持续导出警报和建议。

- [如何配置导出](../security-center/continuous-export.md)

<!--Not Available on - [How to stream alerts into Azure Sentinel](../sentinel/connect-azure-security-center.md)-->

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ir-4-detection-and-analysis---investigate-an-incident"></a>IR-4：检测和分析 - 调查事件

**指南**：确保分析人员在调查潜在事件时可查询和使用不同的数据源，以全面了解所发生的情况。 应收集各种各样的日志，以跟踪整个终止链中潜在攻击者的活动，避免出现盲点。  还应确保收集见解和经验，以供其他分析人员使用和用作将来的历史参考资料。  

用于调查的数据源包括已从作用域内服务和正在运行的系统中收集的集中式日志记录源，但还可以包括以下内容：

- 网络数据 - 使用网络安全组的流日志、Azure 网络观察程序和 Azure Monitor 来捕获网络流日志和其他分析信息。 

- 正在运行的系统的快照： 

    - 使用 Azure 虚拟机的快照功能创建正在运行的系统磁盘的快照。 

    - 使用操作系统的本机内存转储功能来创建正在运行的系统内存的快照。

    - 使用 Azure 服务的快照功能或软件自带的功能来创建正在运行的系统的快照。

Azure Sentinel 提供几乎针对任何日志源的广泛数据分析，并提供一个事例管理门户来管理事件的整个生命周期。 调查过程中的情报信息可与事件相关联，以便进行跟踪和报告。 

- [Windows 计算机的磁盘快照](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux 计算机的磁盘快照](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Azure 支持诊断信息和内存转储收集](https://www.azure.cn/support/legal/support-diagnostic-information-collection/) 

<!--Not Available on - [Investigate incidents with Azure Sentinel](../sentinel/tutorial-investigate-cases.md)-->

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ir-5-detection-and-analysis---prioritize-incidents"></a>IR-5：检测和分析 - 确定事件的优先级

**指南**：根据警报严重性和资产敏感度，为分析人员提供上下文来确定应首要关注哪些事件。 

Azure 安全中心为每条警报分配严重性，方便你根据优先级来确定应该最先调查的警报。 严重性取决于安全中心对调查结果或用于发出警报的分析的可信度，以及对导致警报的活动背后存在恶意意图的可信度级别。

此外，使用标记来标记资源，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)

- [使用标记整理 Azure 资源](https://docs.azure.cn/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ir-6-containment-eradication-and-recovery---automate-the-incident-handling"></a>IR-6：遏制、根除和恢复 - 自动执行事件处理

**指导**：自动执行手动重复性任务来加快响应时间并减轻分析人员的负担。 执行手动任务需要更长的时间，这会导致减慢每个事件的速度，并减少分析人员可以处理的事件数量。 手动任务还会使分析人员更加疲劳，这会增加可导致延迟的人为错误的风险，并降低分析人员专注于复杂任务的工作效率。 使用 Azure 安全中心和 Azure Sentinel 中的工作流自动化功能，可自动触发操作或运行 playbook，对传入的安全警报作出响应。 playbook 执行多项操作，如发送通知、禁用帐户和隔离有问题的网络。 

- [在安全中心配置工作流自动化](../security-center/workflow-automation.md)

- [在 Azure 安全中心设置自动威胁响应](../security-center/tutorial-security-incident.md#triage-security-alerts)

<!--Not Available on - [Set up automated threat responses in Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)-->

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="posture-and-vulnerability-management"></a>安全状况和漏洞管理

<!--Not Available on *For more information, see the [Azure Security Benchmark: Posture and Vulnerability Management](https://docs.azure.cn/security/benchmarks/security-controls-v2-vulnerability-management)-->

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8：执行定期攻击模拟

**指导**：根据需要，对 Azure 资源进行渗透测试或红队活动，并确保修正所有关键安全发现。
请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Azure 政策。 使用 Azure 红队演练策略和执行，并针对 Azure 托管云基础结构、服务和应用程序执行现场渗透测试。

- [Azure 中的渗透测试](../security/fundamentals/pen-testing.md)

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="endpoint-security"></a>终结点安全性

*有关详细信息，请参阅 [Azure 安全基线：终结点安全性](https://docs.azure.cn/security/benchmarks/security-controls-v2-endpoint-security)。*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1：使用终结点检测和响应 (EDR)

**指导**：并未明确允许客户配置终结点检测和响应设置。 但是，Azure 虚拟 WAN 产品中使用的虚拟机确实使用了这些功能。 可通过引用的链接详细了解这些常规功能。 

- [Microsoft Defender 高级威胁防护概述](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [适用于 Windows Server 的 Microsoft Defender ATP 服务](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [适用于非 Windows Server 的 Microsoft Defender ATP 服务](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Azure 安全中心监视**：是

**责任**：共享

## <a name="governance-and-strategy"></a>治理和策略

有关详细信息，请参阅 [Azure 安全基准：治理和策略](https://docs.azure.cn/security/benchmarks/security-controls-v2-governance-strategy)。

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1：定义资产管理和数据保护策略 

**指导**：确保为系统和数据的持续监视和保护记录并传达明确的策略。 确定业务关键数据和系统的发现、评估、保护和监视优先级。 

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

- 与业务风险相符的数据分类标准

- 安全组织对风险和资产清单的洞察力 

- 安全组织对 Azure 服务使用的审批 

- 资产在其生命周期中的安全性

- 与组织数据分类相符的必需访问控制策略

- 使用 Azure 原生的和第三方的数据保护功能

- 传输中数据用例和静态数据用例的数据加密要求

- 合适的加密标准

有关详细信息，请参阅以下资源：
- [Azure 安全体系结构建议 - 存储、数据和加密](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure 安全基础知识 - Azure 数据安全、加密和存储](../security/fundamentals/encryption-overview.md)

- [云采用框架 - Azure 数据安全和加密最佳做法](https://docs.azure.cn/security/fundamentals/data-encryption-best-practices?toc=/cloud-adoption-framework/toc.json&amp;bc=/cloud-adoption-framework/_bread/toc.json)

<!--Not Available on - [Azure Security Benchmark - Asset management](https://docs.azure.cn/security/benchmarks/security-benchmark-v2-asset-management)-->

<!--Not Available on - [Azure Security Benchmark - Data Protection](https://docs.azure.cn/security/benchmarks/security-benchmark-v2-data-protection)-->

**Azure 安全中心监视**：不适用

**责任**：客户

<!--Not Available on - [Guidance on segmentation strategy in Azure (video)](https://docs.azure.cn/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)-->

<!--Not Aavilable on - [Guidance on segmentation strategy in Azure (document)](https://docs.azure.cn/security/compass/governance#enterprise-segmentation-strategy)-->

<!--Not Available on - [Align network segmentation with enterprise segmentation strategy](https://docs.azure.cn/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)-->

<!--Not Available on - [Azure Security Benchmark - Posture and vulnerability management](https://docs.azure.cn/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)-->

<!--Not Available on - [Azure Security Best Practice 1 - People: Educate Teams on Cloud Security Journey](https://docs.azure.cn/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)-->

<!--Not Available on - [Azure Security Best Practice 2 - People: Educate Teams on Cloud Security Technology](https://docs.azure.cn/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)-->

<!--Not Available on - [Azure Security Best Practice 3 - Process: Assign Accountability for Cloud Security Decisions](https://docs.azure.cn/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)-->

### <a name="gs-5-define-network-security-strategy"></a>GS-5：定义网络安全策略

**指导**：制定 Azure 网络安全方法，作为组织的整体安全访问控制策略的一部分。  

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

- 集中化的网络管理和安全职责

- 符合企业分段策略的虚拟网络分段模型

- 各种威胁和攻击场景中的补救策略

- Internet 边缘及入口和出口策略

- 混合云和本地互连策略

- 最新的网络安全项目（例如网络关系图、参考网络体系结构）

有关详细信息，请参阅以下资源：
<!--Not Available on - [Azure Security Best Practice 11 - Architecture. Single unified security strategy](https://docs.azure.cn/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)-->

<!--Not Available on - [Azure Security Benchmark - Network Security](https://docs.azure.cn/security/benchmarks/security-benchmark-v2-network-security)-->

- [Azure 网络安全概述](../security/fundamentals/network-overview.md)

<!--Not Available on - [Enterprise network architecture strategy](https://docs.azure.cn/cloud-adoption-framework/ready/enterprise-scale/architecture)-->

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6：定义标识和特权访问策略

**指导**：制定 Azure 标识和特权访问方法，作为组织的整体安全访问控制策略的一部分。  

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

- 集中化的标识和身份验证系统及其与其他内部和外部标识系统的互连

- 各种用例和条件中的强身份验证方法

- 保护权限高的用户

- 异常用户活动监视和处理  

- 用户标识和访问评审及协调流程

有关详细信息，请参阅以下资源：

<!--Not Available on - [Azure Security Benchmark - Identity management](https://docs.azure.cn/security/benchmarks/security-benchmark-v2-identity-management)-->

<!--Not Available on - [Azure Security Benchmark - Privileged access](https://docs.azure.cn/security/benchmarks/security-benchmark-v2-privileged-access)-->

<!--Not Available on - [Azure Security Best Practice 11 - Architecture. Single unified security strategy](https://docs.azure.cn/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)-->

- [Azure 标识管理安全概述](../security/fundamentals/identity-management-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7：定义日志记录和威胁响应策略

**指南**：建立日志记录和威胁响应策略，以在满足符合性要求的同时快速检测和修正威胁。 优先为分析师提供高质量警报和无缝体验，以便他们能够专注于威胁，而不是执行集成和手动步骤。 

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

- 安全运营 (SecOps) 组织的角色和职责 

- 符合 NIST 或其他行业框架要求的明确定义的事件响应流程 

- 日志捕获和保留，用于支持威胁检测、事件响应和合规性需求

- 使用 SIEM、原生 Azure 功能和其他源，集中查看和关联有关威胁的信息 

- 与客户、供应商和公开的利益相关方之间的通信和通知计划

- 使用 Azure 原生的和第三方的平台进行事件处理，例如日志记录和威胁检测、取证以及攻击补救和根除

- 处理事件和事件后活动的流程，例如经验教训和证据保留

有关详细信息，请参阅以下资源：

<!--Not Available on - [Azure Security Benchmark - Logging and threat detection](https://docs.azure.cn/security/benchmarks/security-benchmark-v2-logging-threat-detection)-->

<!--Not Available on - [Azure Security Benchmark - Incident response](https://docs.azure.cn/security/benchmarks/security-benchmark-v2-incident-response)-->

<!--Not Available on - [Azure Security Best Practice 4 - Process. Update Incident Response Processes for Cloud](https://docs.azure.cn/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)-->

<!--Not Available on - [Azure Adoption Framework, logging, and reporting decision guide](https://docs.azure.cn/cloud-adoption-framework/decision-guides/logging-and-reporting/)-->

<!--Not Available on - [Azure enterprise scale, management, and monitoring](https://docs.azure.cn/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)-->

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](https://docs.azure.cn/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](https://docs.azure.cn/security/benchmarks/security-baselines-overview)

<!-- Update_Description: new article about security baseline -->
<!--NEW.date: 12/14/2020-->