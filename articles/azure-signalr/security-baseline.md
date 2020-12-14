---
title: 适用于 Azure SignalR 服务的 Azure 安全基线
description: Azure SignalR 服务安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: signalr
ms.topic: conceptual
origin.date: 11/25/2020
ms.date: 12/03/2020
ms.author: v-tawe
ms.custom: subject-security-benchmark
ms.openlocfilehash: 5a4d4509ceb1aec24d8305d823b5d51831af67ff
ms.sourcegitcommit: bbe4ee95604608448cf92dec46c5bfe4b4076961
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96598654"
---
# <a name="azure-security-baseline-for-azure-signalr-service"></a>适用于 Azure SignalR 服务的 Azure 安全基线

此安全基线将 [Azure 安全基准版本 2.0](../security/benchmarks/overview.md) 中的指南应用于 Azure SignalR。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。
内容按“安全控制”分组，这些控制根据适用于 Azure SignalR 的 Azure 安全基准和相关指南定义。 排除了不适用于 Azure SignalR 的“控制”。

若要查看 Azure SignalR 如何完全映射到 Azure 安全基准，请参阅[完整的 Azure SignalR 安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](/security/benchmarks/security-controls-v2-network-security)。

<!-- ### NS-1: Implement security for internal traffic

**Guidance**: Microsoft Azure SignalR Service does not support deploying directly into a virtual network, because of this you can not leverage certain networking features with the offering's resources such as network security groups, route tables, or other network dependent appliances such as an Azure Firewall. 

Azure SignalR Service, however, allows you to create private endpoints to secure the traffic between resources in your virtual network and Azure SignalR Service.

You can also use Service tags and configure network security group rules to restrict inbound/outbound traffic to Azure SignalR Service.

- [Use private endpoints for Azure SignalR Service](howto-private-endpoints.md)

- [Configure network access control](howto-network-access-control.md)

- [Use service tags for Azure SignalR Service](howto-service-tags.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer -->

### <a name="ns-2---connect-private-networks-together"></a>NS-2：将专用网络连接在一起  

**指导**：使用专用终结点来保护虚拟网络与 Azure SignalR 服务之间的流量。 选择 Azure ExpressRoute 或 Azure 虚拟专用网 (VPN)，以便在共置环境中的 Azure 数据中心与本地基础结构之间创建专用连接。 

ExpressRoute 连接并不通过公共 Internet，与典型的 Internet 连接相比，它们的可靠性更高、速度更快且延迟时间更短。 对于点到站点 VPN 和站点到站点 VPN，可使用这些 VPN 选项的任意组合以及 Azure ExpressRoute 将本地设备或网络连接到虚拟网络。

若要将 Azure 中的两个或更多虚拟网络连接在一起，请使用虚拟网络对等互连。 对等互连虚拟网络之间的网络流量是专用的，且保留在 Azure 主干网络上。

<!-- - [Use private endpoints for Azure SignalR Service](howto-private-endpoints.md) -->

- [什么是 ExpressRoute 连接模型](../expressroute/expressroute-connectivity-models.md)

- [Azure VPN 概述](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [虚拟网络对等](../virtual-network/virtual-network-peering-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

<!-- ### NS-3: Establish private network access to Azure services

**Guidance**: Use Azure Private Link to enable private access to Azure SignalR Service from your virtual networks without crossing the internet.

Private access is an additional defense in depth measure in addition to authentication and traffic security offered by Azure services.

- [Understand Azure Private Link](../private-link/private-link-overview.md)

- [Use private endpoints for Azure SignalR Service](howto-private-endpoints.md)

- [Configure network access control](howto-network-access-control.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer -->

### <a name="ns-6-simplify-network-security-rules"></a>NS-6：简化网络安全规则

**指导**：使用 Azure 虚拟网络服务标记，在为 Azure SignalR 服务资源配置的网络安全组或 Azure 防火墙上定义网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 通过在规则的相应“源”或“目标”字段中指定服务标记名称（例如：AzureSignalR），可允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

- [了解并使用服务标记](../virtual-network/service-tags-overview.md)

<!-- - [Use service tags for Azure SignalR Service](howto-service-tags.md) -->

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="identity-management"></a>标识管理

有关详细信息，请参阅 [Azure 安全基准：标识管理](/security/benchmarks/security-controls-v2-identity-management)。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：将 Azure Active Directory 标准化为中央标识和身份验证系统

**指导**：Azure SignalR 服务使用 Azure Active Directory (Azure AD) 作为默认标识和访问管理服务。 你应该使 Azure AD 标准化，以便控制组织在以下资源中的标识和访问管理：

- Microsoft 云资源，如 Azure 门户、Azure 存储、Azure 虚拟机（Linux 和 Windows）、Azure Key Vault、PaaS 和 SaaS 应用程序。
- 你的组织的资源，例如 Azure 上的应用程序，或公司网络资源。

Azure SignalR 服务仅对管理平面支持 Azure AD 身份验证；对于数据平面，则不支持 Azure AD 身份验证。 下面是 Azure SignalR 服务中的内置角色列表：

- SignalR 参与者
- SignalR AccessKey 读取者

在组织的云安全做法中，应优先处理 Azure AD 保护事宜。 Azure AD 提供标识安全分数，帮助你评估与 Microsoft 的最佳做法建议相关的标识安全状况。 使用评分来估计你的配置与最佳做法建议的匹配程度，并改善你的安全状况。

Azure AD 支持外部标识，以使没有 Microsoft 帐户的用户可以使用其外部标识登录到其应用程序和资源。

- [Azure Active Directory 中的租赁](../active-directory/develop/single-and-multi-tenant-apps.md)

- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

<!-- - [Use external identity providers for application](/active-directory/b2b/identity-providers) -->

<!-- - [What is the identity secure score in Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md) -->

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2：安全且自动地管理应用程序标识

**指导**：Azure SignalR 服务将 Azure 托管标识用于非人为帐户，例如在无服务器方案中调用上游方的帐户。 建议使用 Azure 托管标识功能访问其他 Azure 资源。 Azure SignalR 服务可以通过预定义的访问授权规则以原生方式向支持 Azure Active Directory (Azure AD) 身份验证的 Azure 服务或资源进行身份验证，而无需使用在源代码或配置文件中硬编码的凭据。

- [Azure 托管标识](../active-directory/managed-identities-azure-resources/overview.md)

- [支持 Azure 资源托管标识的服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) 

- [Azure SignalR 服务的托管标识](howto-use-managed-identity.md)

**Azure 安全中心监视**：不适用

**责任**：客户

<!-- ### IM-3: Use Azure AD single sign-on (SSO) for application access

**Guidance**: Azure SignalR Service uses Azure Active Directory (Azure AD) to provide identity and access management to SignalR resources. This includes enterprise identities such as employees, as well as external identities such as partners, vendors, and suppliers. This enables single sign-on to manage and secure access to your organization’s data and resources on-premises and in the cloud. Connect all your users, applications, and devices to the Azure AD for seamless, secure access and greater visibility and control.

- [Understand Application SSO with Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer -->

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：对所有基于 Azure Active Directory 的访问使用强身份验证控制

**指导**：Azure SignalR 服务使用 Azure Active Directory (Azure AD)，后者支持通过多重身份验证进行的强身份验证控制，并且支持强无密码方法。

- 多重身份验证 - 启用 Azure AD 多重身份验证并遵循 Azure 安全中心标识和访问管理建议，以在多重身份验证设置中执行适用的最佳做法。 可对所有用户或选定用户强制执行多重身份验证，也可以根据登录条件和风险因素在每个用户级别强制执行多重身份验证。

- 无密码身份验证 - 提供三个无密码身份验证选项：Windows Hello 企业版、Microsoft Authenticator 应用和本地身份验证方法（如智能卡）。

对于管理员和特权用户，请确保使用最高级别的强身份验证方法，然后向其他用户推出适当的强身份验证策略。

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

<!-- - [Introduction to passwordless authentication options for Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md) -->

- [Azure AD 默认密码策略](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

<!-- - [Eliminate bad passwords using Azure Active Directory Password Protection](../active-directory/authentication/concept-password-ban-bad.md) -->

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：监视并提醒帐户异常

**指导**：Azure SignalR 服务已与提供以下数据源的 Azure Active Directory 集成：

- 登录 - 登录报告提供有关托管应用程序使用情况和用户登录活动的信息。

- 审核日志 - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如添加或删除用户、应用、组、角色和策略）所做的更改。

- 风险登录 - 风险登录是指可能由并非用户帐户合法拥有者的某人进行的登录尝试。

- 已标记为存在风险的用户 - 风险用户是指可能已泄露的用户帐户。

这些数据源可与 Azure Monitor、Azure Sentinel 或第三方安全事件和信息管理 (SIEM) 系统集成。

Azure 安全中心还可针对某些可疑活动发出警报，例如，失败的身份验证尝试次数过多，使用了订阅中的已弃用帐户。

Azure 高级威胁防护 (ATP) 是一种安全解决方案，它可使用 Active Directory 信号来识别、检测和调查高级威胁、泄露的标识以及恶意的内部操作。

- [Azure Active Directory 中的“审核活动”报表](../active-directory/reports-monitoring/concept-audit-logs.md)

- [如何在 Azure 安全中心内监视用户的标识和访问活动](../security-center/security-center-identity-access.md)

- [如何将 Azure 活动日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：基于条件限制 Azure 资源访问

**指导**：Azure SignalR 服务支持 Azure Active Directory (Azure AD) 条件访问，以实现基于用户定义的条件进行更精细的访问控制，例如，从特定 IP 范围登录的用户将需要使用 MFA 进行登录。 精细身份验证会话管理策略还可用于不同的用例。

- [Azure 条件访问概述](../active-directory/conditional-access/overview.md)

- [常见的条件访问策略](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

<!-- - [Configure authentication session management with conditional access](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md) -->

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="privileged-access"></a>特权访问

有关详细信息，请参阅 [Azure 安全基准：特权访问](/security/benchmarks/security-controls-v2-privileged-access)。

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1：保护和限制具有较高权限的用户

**指导**：Azure Active Directory (Azure AD) 中最重要的内置角色是全局管理员和特权角色管理员，因为分配到这两种角色的用户可以委托管理员角色：

- 全局管理员/公司管理员：具有此角色的用户可访问 Azure AD 中的所有管理功能，还可访问使用 Azure AD 标识的服务。

- 特权角色管理员：具有此角色的用户可以管理 Azure Active Directory (Azure AD) 和 Azure AD Privileged Identity Management (PIM) 中的角色分配。 此外，该角色可管理 PIM 和管理单元的各个方面。

Azure SignalR 服务具有内置的高特权角色。 请限制高特权帐户或角色的数量并在提升的级别保护这些 Azure AD 帐户，因为具有此特权的用户可以直接或间接地读取和修改 Azure 环境中的每个资源。

你可使用Azure AD Privileged Identity Management (PIM) 提供对 Azure 资源和 Azure AD 的实时 (JIT) 特权访问权限。 JIT 仅在用户需要执行特权任务时授予临时权限。 当 Azure AD 组织中存在可疑或不安全的活动时，PIM 还会生成安全警报。

- [SignalR 参与者](../role-based-access-control/built-in-roles.md#signalr-contributor)

- [Azure AD 中的管理角色权限](/active-directory/users-groups-roles/directory-assign-admin-roles)

- [使用 Azure Privileged Identity Management 安全警报](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

<!-- - [Securing privileged access for hybrid and cloud deployments in Azure AD](/active-directory/users-groups-roles/directory-admin-roles-secure) -->

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制对关键业务型系统的管理访问权限

**指导**：Azure SignalR 服务使用 Azure 基于角色的访问控制 (Azure RBAC)，通过限制向哪些帐户授予对其所属的订阅和管理组的特权访问权限，来隔离对业务关键型系统的访问。

确保还限制了对业务关键型资产具有管理访问权限的管理、标识和安全系统的访问，这些资产包括在业务关键型系统上安装了代理的 Active Directory 域控制器、安全工具和系统管理工具。 入侵这些管理和安全系统的攻击者可以立即将它们用作损害业务关键型资产的武器。

所有类型的访问控制都应符合企业分段策略，确保访问控制保持一致。

<!-- - [Azure Components and Reference model](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) -->

- [访问管理组](../governance/management-groups/overview.md#management-group-access)

<!-- - [Azure subscription administrators](../cost-management-billing/manage/add-change-subscription-administrator.md) -->

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期审查和协调用户访问权限

**指导**：定期审查用户帐户和访问权限分配，确保帐户及其访问权限级别均有效。

Azure SignalR 服务使用 Azure Active Directory (Azure AD) 帐户来定期管理其资源、审阅用户帐户和访问权限分配，以确保帐户及其访问权限有效。 可使用 Azure AD 访问评审来审查组成员身份、对企业应用程序的访问权限和角色分配。 Azure AD 报告提供日志来帮助发现过时的帐户。 你还可使用 Azure AD Privileged Identity Management 来创建访问评审报表工作流以便于执行评审。

Azure SignalR 服务中的内置角色包括：

- SignalR 参与者
- SignalR AccessKey 读取者

此外，Azure Privileged Identity Management 还可配置为在创建过多管理员帐户时发出警报，并识别过时或配置不正确的管理员帐户。

- [在 Privileged Identity Management (PIM) 中创建对 Azure 资源角色的访问评审](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [如何使用 Azure AD 标识和访问评审](../active-directory/governance/access-reviews-overview.md)

- [SignalR 参与者](../role-based-access-control/built-in-roles.md#signalr-contributor)

- [SignalR AccessKey 读取者](../role-based-access-control/built-in-roles.md#signalr-accesskey-reader)

-

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4：在 Azure AD 中设置紧急访问

**指导**：Azure SignalR 服务使用 Azure Active Directory (Azure AD) 管理其资源。 为了防止意外退出 Azure AD 组织，请设置一个紧急访问帐户，以便在正常管理帐户无法使用时进行访问。 紧急访问帐户通常拥有较高的权限，因此请不要将其分配给特定的个人。 紧急访问帐户只能用于“不受限”紧急情况，即不能使用正常管理帐户的情况。

应确保妥善保管紧急访问帐户的凭据（例如密码、证书或智能卡），仅将其告诉只能在紧急情况下有权使用它们的个人。

- [在 Azure AD 中管理紧急访问帐户](/active-directory/users-groups-roles/directory-emergency-access)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pa-5-automate-entitlement-management"></a>PA-5：将权利管理自动化 

**指导**：Azure SignalR 服务已与 Azure Active Directory (Azure AD) 集成，以管理其资源。 使用 Azure AD 的权利管理功能可自动执行访问请求工作流，包括访问权限分配、审查和过期。 还支持两阶段或多阶段审批。

- [什么是 Azure AD 访问评审](../active-directory/governance/access-reviews-overview.md)

- [什么是 Azure AD 权利管理](../active-directory/governance/entitlement-management-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

<!-- ### PA-6: Use privileged access workstations

**Guidance**: Secured, isolated workstations are critically important for the security of sensitive roles like administrators, developers, and critical service operators. Use highly secured user workstations and/or Azure Bastion for administrative tasks. Use Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP), and/or Microsoft Intune to deploy a secure and managed user workstation for administrative tasks. The secured workstations can be centrally managed to enforce secured configuration including strong authentication, software and hardware baselines, restricted logical and network access.

- [Understand privileged access workstations](../active-directory/devices/concept-azure-managed-workstation.md)

- [Deploy a privileged access workstation](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer -->

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7：遵循 Just Enough Administration（最小特权原则） 

**指导**：SignalR 服务已与 Azure 基于角色的访问控制 (Azure RBAC) 集成，以管理其资源。 使用 Azure RBAC，可通过角色分配来管理 Azure 资源访问。 可将这些角色分配给用户、组服务主体和托管标识。 某些资源有预定义的内置角色，可以通过工具（例如 Azure CLI、Azure PowerShell 或 Azure 门户）来清点或查询这些角色。 

通过 Azure RBAC 分配给资源的特权应始终限制为角色所需的特权。 这是对 Azure AD Privileged Identity Management (PIM) 的实时 (JIT) 方法的补充，应定期进行审查。

SignalR 服务中的内置角色：

- SignalR 参与者
- SignalR AccessKey 读取者

请使用内置角色来分配权限，仅在必要时创建自定义角色。

- [什么是 Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md)

- [如何在 Azure 中配置 RBAC](../role-based-access-control/role-assignments-portal.md)

- [如何使用 Azure AD 标识和访问评审](../active-directory/governance/access-reviews-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](/security/benchmarks/security-controls-v2-data-protection)。

### <a name="dp-2-protect-sensitive-data"></a>DP-2：保护敏感数据

**指导**：使用 Azure 基于角色的访问控制 (Azure RBAC)、基于网络的访问控制以及 Azure 服务中的特定控制（例如 SQL 和其他数据库中的加密）来限制访问，从而保护敏感数据。

为了确保一致的访问控制，所有类型的访问控制都应符合企业分段策略。 企业分段策略还应根据敏感的或业务关键型的数据和系统的位置来确定。

对于 Microsoft 管理的基础平台，Microsoft 会将所有客户内容视为敏感数据，全方位防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据始终安全，Microsoft 实施了一些默认的数据保护控制机制和功能。

- [Azure 基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md)

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="asset-management"></a>资产管理

有关详细信息，请参阅 [Azure 安全基准：资产管理](/security/benchmarks/security-controls-v2-asset-management)。

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1：确保安全团队可以了解与资产相关的风险

**指南**：确保在 Azure 租户和订阅中向安全团队授予了安全读取者权限，以便他们可以使用 Azure 安全中心监视安全风险。 

根据安全团队责任划分方式的不同，监视安全风险可能是中心安全团队或本地团队的责任。 也就是说，安全见解和风险必须始终在组织内集中聚合。 

安全读取者权限可以广泛应用于整个租户（根管理组），也可以限制到管理组或特定订阅。 

注意：若要了解工作负载和服务，可能需要更多权限。 

- [安全读取者角色概述](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理组概述](../governance/management-groups/overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2：确保安全团队有权访问资产清单和元数据

**指导**：将标记应用到 Azure 资源、资源组和订阅，以便有条理地将它们组织成分类。 每个标记均由名称和值对组成。 例如，可以对生产中的所有资源应用名称“Environment”和值“Production”。

- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md)

- [Azure 安全中心资产库存管理](../security-center/asset-inventory.md)

<!-- - [For more information about tagging assets, see the resource naming and tagging decision guide](https://docs.azure.cn/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure-resource-manager/management/toc.json) -->

**Azure 安全中心监视**：是

**责任**：客户

### <a name="am-3-use-only-approved-azure-services"></a>AM-3：仅使用已批准的 Azure 服务

**指导**：请使用 Azure Policy 来审核和限制用户可以在你的环境中预配哪些服务。 使用 Azure Resource Graph 查询和发现订阅中的资源。 你也可以使用 Azure Monitor 来创建规则，以便在检测到未经批准的服务时触发警报。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](../governance/policy/samples/built-in-policies.md#general)

- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4：确保资产生命周期管理的安全

**指南**：建立或更新安全策略，用于应对资产生命周期管理过程的可能具有重大影响的修改。 这些修改包括但不限于对以下内容的更改：标识提供者和访问权限、数据敏感度、网络配置，以及管理特权分配。 在 Azure SignalR 服务中，这些更改包括：重新生成访问密钥、创建/更新专用终结点、管理网络访问控制。

如果不再需要 Azure 资源，请将其删除。

- [删除 Azure 资源组和资源](../azure-resource-manager/management/delete-resource-group.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5：限制用户与 Azure 资源管理器进行交互的能力

**指导**：通过为“Microsoft Azure 管理”应用配置“阻止访问”，使用 Azure 条件访问来限制用户与 Azure 资源管理器交互的能力。

- [如何配置条件访问来阻止对 Azure 资源管理器的访问](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-threat-detection"></a>日志记录和威胁检测

有关详细信息，请参阅 [Azure 安全基准：日志记录和威胁检测](/security/benchmarks/security-controls-v2-logging-threat-protection)。

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2：启用 Azure 标识和访问管理的威胁检测

**指导**：Azure Active Directory (Azure AD) 提供以下用户日志，这些日志可在 Azure AD 报告中查看，也可与 Azure Monitor、Azure Sentinel 或其他 SIEM/监视工具集成，以用于更复杂的监视和分析用例：

- 登录 - 登录报告提供有关托管应用程序使用情况和用户登录活动的信息。

- 审核日志 - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如添加或删除用户、应用、组、角色和策略）所做的更改。

- 风险登录 - 风险登录是指可能由并非用户帐户合法拥有者的某人进行的登录尝试。
- 已标记为存在风险的用户 - 风险用户是指可能已泄露的用户帐户。

Azure 安全中心还可针对某些可疑活动发出警报，例如，失败的身份验证尝试次数过多，使用了订阅中的已弃用帐户。 除了基本的安全机制监视，Azure 安全中心的威胁防护模块还可从各个 Azure 计算资源（虚拟机、容器、应用服务）、数据资源（SQL 数据库和存储）和 Azure 服务层收集更深入的安全警报。 通过此功能，可查看各个资源内的帐户异常情况。

- [Azure Active Directory 中的“审核活动”报表](../active-directory/reports-monitoring/concept-audit-logs.md)

<!-- - [Enable Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) -->

- [Azure 安全中心的威胁防护](/security-center/threat-protection)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3：为 Azure 网络活动启用日志记录

**指导**：Azure SignalR 服务不适合部署到虚拟网络中，因此你无法启用网络安全组流日志记录、通过防火墙路由流量或执行数据包捕获。

但是，Azure SignalR 服务会记录它针对客户访问而处理的网络流量。 在已部署的产品/服务资源中启用资源日志，并将这些日志配置为发送到存储帐户，以供长期保留和审核。

- [Azure SignalR 服务的资源日志](signalr-howto-diagnostic-logs.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：为 Azure 资源启用日志记录

**指导**：自动可用的活动日志包含用于 Azure SignalR 服务资源的所有写入操作（PUT、POST、DELETE），但读取操作 (GET) 除外。 活动日志可用于在进行故障排除时查找错误，或监视组织中的用户如何对资源进行修改。

请为 Azure SignalR 服务启用 Azure 资源日志。 可以使用 Azure 安全中心和 Azure Policy 来启用资源日志和日志数据收集。 这些日志可能对日后调查安全事件和执行取证演练至关重要。

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md)

- [了解 Azure 中的日志记录和不同的日志类型](../azure-monitor/platform/platform-logs-overview.md)

- [Azure SignalR 服务的资源日志](signalr-howto-diagnostic-logs.md)

- [了解 Azure 安全中心数据收集](../security-center/security-center-enable-data-collection.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：集中管理和分析安全日志

**指导**：集中记录存储和分析来实现关联。 对于每个日志源，请确保已分配数据所有者、访问指南、存储位置、用于处理和访问数据的工具以及数据保留要求。

确保正在将 Azure 活动日志集成到中央日志记录。 通过 Azure Monitor 引入日志，以聚合终结点设备、网络资源和其他安全系统生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期存档存储。

另外，请启用 Azure Sentinel 或第三方安全信息和事件管理 (SIEM) 系统并将数据载入其中。

许多组织选择将 Azure Sentinel 用于频繁使用的“热”数据，并将 Azure 存储用于不太频繁使用的“冷”数据。

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md)

<!-- - [How to onboard Azure Sentinel](../sentinel/quickstart-onboard.md) -->

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](/security/benchmarks/security-controls-v2-incident-response)。

<!-- ### IR-1: Preparation – update incident response process for Azure

**Guidance**: Ensure your organization has processes to respond to security incidents, has updated these processes for Azure, and is regularly exercising them to ensure readiness.

- [Implement security across the enterprise environment](/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Incident response reference guide](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer -->

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2：准备 - 设置事件通知

**指导**：在 Azure 安全中心中设置安全事件联系人信息。 如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的一方访问了你的数据，Microsoft 将使用此联系信息来与你取得联系。 还可以选择基于事件响应需求在不同的 Azure 服务中自定义事件警报和通知。 

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：检测和分析 - 根据高质量警报创建事件

**指南**：确保你有创建高质量警报和衡量警报质量的流程。 这样，你就可以从过去的事件中吸取经验，并为分析人员确定警报的优先级，这样他们就不会浪费时间来处理误报。 

可以基于从过去的事件中吸取的经验、经过验证的社区来源以及各种工具来生成高质量警报，这些工具旨在通过融合和关联各种信号源来生成和清除警报。 

Azure 安全中心可跨许多 Azure 资产提供高质量的警报。 可以使用 ASC 数据连接器将警报流式传输到 Azure Sentinel。 借助 Azure Sentinel，可创建高级警报规则来自动生成事件以进行调查。 

使用导出功能导出 Azure 安全中心警报和建议，以帮助识别 Azure 资源的风险。 手动导出或持续导出警报和建议。

- [如何配置导出](../security-center/continuous-export.md)

<!-- - [How to stream alerts into Azure Sentinel](../sentinel/connect-azure-security-center.md) -->

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4：检测和分析 - 调查事件

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

<!-- - [Microsoft Azure Support diagnostic information and memory dump collection](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/)  -->

<!-- - [Investigate incidents with Azure Sentinel](../sentinel/tutorial-investigate-cases.md) -->

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5：检测和分析 - 确定事件优先级

**指南**：根据警报严重性和资产敏感度，为分析人员提供上下文来确定应首要关注哪些事件。 

Azure 安全中心为每条警报分配严重性，方便你根据优先级来确定应该最先调查的警报。 严重性取决于安全中心对调查结果或用于发出警报的分析的可信度，以及对导致警报的活动背后存在恶意意图的可信度级别。

此外，使用标记来标记资源，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)

- [使用标记整理 Azure 资源](/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：包含、根除和恢复 - 自动执行事件处理

**指导**：自动执行手动重复性任务来加快响应时间并减轻分析人员的负担。 执行手动任务需要更长的时间，这会导致减慢每个事件的速度，并减少分析人员可以处理的事件数量。 手动任务还会使分析人员更加疲劳，这会增加可导致延迟的人为错误的风险，并降低分析人员专注于复杂任务的工作效率。 使用 Azure 安全中心和 Azure Sentinel 中的工作流自动化功能，可自动触发操作或运行 playbook，对传入的安全警报作出响应。 playbook 执行多项操作，如发送通知、禁用帐户和隔离有问题的网络。 

- [在安全中心配置工作流自动化](../security-center/workflow-automation.md)

- [在 Azure 安全中心设置自动威胁响应](../security-center/tutorial-security-incident.md#triage-security-alerts)

<!-- - [Set up automated threat responses in Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md) -->

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="posture-and-vulnerability-management"></a>安全状况和漏洞管理

<!-- *For more information, see the [Azure Security Benchmark: Posture and Vulnerability Management](/security/benchmarks/security-controls-v2-vulnerability-management).* -->

<!-- ### PV-1: Establish secure configurations for Azure services 

**Guidance**: Azure SignalR Service supports below service-specific policies that are available in Azure Security Center to audit and enforce configurations of your Azure resources. This can be configured in Azure Security Center or with Azure Policy initiatives.

You can use Azure Blueprints to automate deployment and configuration of services and application environments including Azure Resources Manager templates, Azure role-based access controls (Azure RBAC) controls, and policies, in a single blueprint definition.

- [Working with security policies in Azure Security Center](../security-center/tutorial-security-policy.md)

- [Audit compliance of Azure SignalR Service resources using Azure Policy](signalr-howto-azure-policy.md)

- [Tutorial - Create and manage policies to enforce compliance](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Blueprints](../governance/blueprints/overview.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer -->

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2：为所有 Azure 服务维护安全配置

**指导**：使用 Azure 安全中心监视配置基线，并强制使用 Azure Policy 的 [拒绝] 和 [如果不存在便部署] 规则强制实施安全的 Azure SignalR 服务配置。 Azure SignalR 服务策略包括：

访问所引用的链接可获得更多信息。

- [使用 Azure Policy 审核 Azure SignalR 服务资源的合规性](signalr-howto-azure-policy.md)

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

- [创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3：为计算资源建立安全配置

**指导**：使用 Azure 安全中心和 Azure Policy 建立 Azure SignalR 服务的安全配置。

- [如何监视 Azure 安全中心建议](../security-center/security-center-recommendations.md)

- [安全建议 - 参考指南](../security-center/recommendations-reference.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8：执行定期攻击模拟

**指导**：根据需要，对 Azure 资源进行渗透测试或红队活动，并确保修正所有关键安全发现。
请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。

- [Azure 中的渗透测试](../security/fundamentals/pen-testing.md)

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="backup-and-recovery"></a>备份和恢复

有关详细信息，请参阅 [Azure 安全基准：备份和恢复](/security/benchmarks/security-controls-v2-backup-recovery)。

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4：减少密钥丢失风险

**指导**：确保你有适当的措施来防止和恢复丢失的密钥。 在 Azure Key Vault 中启用软删除和清除保护，以防止意外删除或恶意删除密钥。

- [如何在 Key Vault 中启用软删除和清除保护](https://docs.azure.cn/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

<!-- ## Governance and Strategy -->

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/security/benchmarks/security-baselines-overview)
