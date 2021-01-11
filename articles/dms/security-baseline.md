---
title: 适用于 Azure 数据库迁移服务的 Azure 安全基线
description: Azure 数据库迁移服务安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: WenJason
ms.service: dms
ms.topic: conceptual
origin.date: 12/08/2020
ms.date: 01/04/2021
ms.author: v-jay
ms.custom: subject-security-benchmark
ms.openlocfilehash: 992bf3fe95c5e77fa96107eb4c7d24d598d04cb0
ms.sourcegitcommit: b4fd26098461cb779b973c7592f951aad77351f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857617"
---
# <a name="azure-security-baseline-for-azure-database-migration-service"></a>适用于 Azure 数据库迁移服务的 Azure 安全基线

此安全基线将 [Azure 安全基准 2.0 版](../security/benchmarks/overview.md)中的指南应用到 Azure 数据库迁移服务。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容按“安全控制”分组，这些控制根据适用于 Azure 数据库迁移服务的 Azure 安全基准和相关指南定义。 排除了不适用于 Azure 数据库迁移服务的“控制”。

若要了解 Azure 数据库迁移服务如何完全映射到 Azure 安全基准，请参阅[完整的 Azure 数据库迁移服务安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](/security/benchmarks/security-controls-v2-network-security)。

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1：实现内部流量的安全性

**指导**：部署 Azure 数据库迁移服务资源时，必须创建或使用现有虚拟网络。 确保所有 Azure 虚拟网络都遵循与业务风险相匹配的企业分段原则。 任何可能会给组织带来更高风险的系统都应隔离在其自己的虚拟网络中，并通过网络安全组 (NSG) 和/或 Azure 防火墙进行充分保护。

默认情况下，Azure 数据库迁移服务使用 TLS 1.2。 可以在 Azure 数据库迁移服务的服务配置边栏选项卡中启用对 TLS 1.0 或 TLS 1.1 的支持（如果这是要迁移的数据源实现后向兼容性所需的）。

- [如何创建包含安全规则的网络安全组](../virtual-network/tutorial-filter-network-traffic.md)

 

- [如何部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md) 

- [按此处提供的要为 Azure 数据库迁移服务打开的端口的先决条件文档操作](pre-reqs.md#prerequisites-common-across-migration-scenarios)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ns-2-connect-private-networks-together"></a>NS-2：将专用网络连接在一起

**指导**：如果你的迁移用例涉及跨网络流量，则可选择在归置环境中使用 Azure ExpressRoute 或 Azure 虚拟专用网 (VPN) 在 Azure 数据中心与本地基础结构之间创建专用连接。 ExpressRoute 连接并不通过公共 Internet，与典型的 Internet 连接相比，它们的可靠性更高、速度更快且延迟时间更短。 对于点到站点 VPN 和站点到站点 VPN，可使用这些 VPN 选项的任意组合以及 Azure ExpressRoute 将本地设备或网络连接到虚拟网络。 若要将 Azure 中的两个或更多虚拟网络连接在一起，请使用虚拟网络对等互连。 对等互连虚拟网络之间的网络流量是专用的，且保留在 Azure 主干网络上。

- [什么是 ExpressRoute 连接模型](../expressroute/expressroute-connectivity-models.md) 

- [Azure VPN 概述](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [虚拟网络对等](../virtual-network/virtual-network-peering-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3：建立对 Azure 服务的专用网络访问

**指导**：在适用的情况下，使用 Azure 专用链接在迁移过程中启用对源和目标服务（例如 Azure SQL Server 或其他必需服务）的专用访问。  在 Azure 专用链接尚不可用的情况下，请使用 Azure 虚拟网络服务终结点。  借助 Azure 专用链接和服务终结点，可通过 Azure 主干网络上的优化路由对服务进行安全访问，不需要通过 Internet。  

此外，在虚拟网络上预配 Azure 数据库迁移服务之前，请确保满足先决条件，包括需要启用的通信端口。 

- [使用 Azure 数据库迁移服务的先决条件概述](pre-reqs.md)

- [了解虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="identity-management"></a>标识管理

有关详细信息，请参阅 [Azure 安全基准：标识管理](/security/benchmarks/security-controls-v2-identity-management)。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：将 Azure Active Directory 标准化为中央标识和身份验证系统

**指导**：Azure 数据库迁移服务使用 Azure Active Directory (Azure AD) 作为默认标识和访问管理服务。 你应该使 Azure AD 标准化，以便控制组织在以下资源中的标识和访问管理：Microsoft 云资源，如 Azure 门户、Azure 存储、Azure 虚拟机（Linux 和 Windows）、Azure Key Vault、PaaS 和 SaaS 应用程序。

你的组织的资源，例如 Azure 上的应用程序，或公司网络资源。

在组织的云安全做法中，应优先处理 Azure AD 保护事宜。 Azure AD 提供标识安全分数，让你可以根据 Azure 的最佳做法建议来评估标识安全状况。 使用评分来估计你的配置与最佳做法建议的匹配程度，并改善你的安全状况。

- [Azure Active Directory 中的租赁](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

**Azure 安全中心监视**：是

**责任**：客户

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2：安全且自动地管理应用程序标识

**指导**：Azure 数据库迁移服务要求用户在 Azure Active Directory (Azure AD) 中创建应用程序 ID（服务主体）和身份验证密钥，以便在“联机”模式下迁移到 Azure SQL 数据库托管实例。 此应用程序 ID 要求在订阅级别具有“参与者”角色（不建议这样做，因为这样会授予“参与者”角色过多的访问权限），或者要求创建自定义角色，这些角色具有的特定权限是 Azure 数据库迁移服务所要求的。

建议在迁移完成后删除此应用程序 ID。

- [应用程序 ID 在联机迁移模式下如何使用](tutorial-sql-server-managed-instance-online.md)

- [用于从 SQL Server 在线迁移到 Azure SQL 托管实例的自定义角色](resource-custom-roles-sql-db-managed-instance.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：对所有基于 Azure Active Directory 的访问使用强身份验证控制

**指导**：Azure 数据库迁移服务使用 Azure Active Directory，后者支持通过多重身份验证进行的强身份验证控制，并且支持强无密码方法。
多重身份验证 - 启用 Azure AD 多重身份验证并遵循 Azure 安全中心标识和访问管理建议，以在多重身份验证设置中推行一些最佳做法。 可对所有用户或选定用户强制执行多重身份验证，也可根据登录条件和风险因素在每个用户级别这样做。

对于管理员和特权用户，请确保使用最强级别的身份验证，然后将相应的强身份验证策略推广到其他用户。

- [如何在 Azure 中启用多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure AD 默认密码策略](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts) 

**Azure 安全中心监视**：是

**责任**：客户

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：监视并提醒帐户异常

**指导**：Azure 数据库迁移服务已与提供以下数据源的 Azure Active Directory 集成：登录 - 登录报告提供的信息涉及托管应用程序的使用情况和用户登录活动。

审核日志 - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如添加或删除用户、应用、组、角色和策略）所做的更改。

这些数据源可以与 Azure Monitor 或第三方 SIEM 系统集成。

Azure 高级威胁防护 (ATP) 是一种安全解决方案，它可使用 Active Directory 信号来识别、检测和调查高级威胁、泄露的标识以及恶意的内部操作。

- [Azure Active Directory 中的“审核活动”报表](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [如何在 Azure 安全中心内监视用户的标识和访问活动](../security-center/security-center-identity-access.md) 

- [如何将 Azure 活动日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：基于条件限制 Azure 资源访问

**指导**：Azure 数据库迁移服务使用 Azure AD 进行身份验证，其中的条件访问可用于根据用户定义的条件进行更细粒度的访问控制，例如，来自特定 IP 范围的用户登录名需要使用多重身份验证进行登录。

- [Azure 条件访问概述](../active-directory/conditional-access/overview.md) 

- [常见的条件访问策略](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

**Azure 安全中心监视**：是

**责任**：客户

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7：消除意外的凭据透露

**指导**：Azure 数据库迁移服务允许客户部署/运行代码、配置或持久化数据（可能包含标识/机密）。建议你通过凭据扫描程序来识别代码、配置或持久化数据中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

如果使用 GitHub，你可以通过原生的机密扫描功能来识别代码中的凭据或其他形式的机密。

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [GitHub 机密扫描](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="privileged-access"></a>特权访问

有关详细信息，请参阅 [Azure 安全基准：特权访问](/security/benchmarks/security-controls-v2-privileged-access)。

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期审查和协调用户访问权限

**指导**：Azure 数据库迁移服务要求用户在 Azure Active Directory (Azure AD) 中创建应用程序 ID（服务主体）和身份验证密钥，以便在“联机”模式下迁移到 Azure SQL 数据库托管实例。 建议在迁移完成后删除此应用程序 ID。

- [应用程序 ID 在联机迁移模式下如何使用](tutorial-sql-server-managed-instance-online.md)

- [用于从 SQL Server 在线迁移到 Azure SQL 托管实例的自定义角色](resource-custom-roles-sql-db-managed-instance.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4：在 Azure AD 中设置紧急访问

**指导**：为了防止被意外锁定在 Azure AD 组织外，常规做法是在 Azure 数据库迁移服务外部设置一个紧急访问帐户，以便在正常的管理帐户无法使用时进行访问。 紧急访问帐户通常拥有较高的权限，因此请不要将其分配给特定的个人。 紧急访问帐户只能用于“不受限”紧急情况，即不能使用正常管理帐户的情况。

应确保妥善保管紧急访问帐户的凭据（例如密码、证书或智能卡），仅将其告诉只能在紧急情况下有权使用它们的个人。

- [在 Azure AD 中管理紧急访问帐户](/active-directory/users-groups-roles/directory-emergency-access)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="pa-5-automate-entitlement-management"></a>PA-5：将权利管理自动化 

**指导**：Azure 数据库迁移服务已与 Azure Active Directory 集成来管理其资源。 使用 Azure AD 的权利管理功能可自动执行访问请求工作流，包括访问权限分配、审查和过期。 还支持两阶段或多阶段审批。

- [什么是 Azure AD 访问评审](../active-directory/governance/access-reviews-overview.md) 

- [什么是 Azure AD 权利管理](../active-directory/governance/entitlement-management-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7：遵循 Just Enough Administration（最小特权原则） 

**指导**：Azure 数据库迁移服务已与 Azure 基于角色的访问控制 (RBAC) 集成来管理其资源。 使用 Azure RBAC，可通过角色分配来管理 Azure 资源访问。 可以将这些角色分配给用户、组服务主体和托管标识。 某些资源具有预定义的内置角色，可以通过工具（例如 Azure CLI、Azure PowerShell 或 Azure 门户）来清点或查询这些角色。 通过 Azure RBAC 分配给资源的特权应始终限制为角色所需的特权。 这是对 Azure AD Privileged Identity Management (PIM) 的实时 (JIT) 方法的补充，应定期进行审查。

请使用内置角色来分配权限，仅在必要时创建自定义角色。

- [什么是 Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md)

- [如何在 Azure 中配置 RBAC](../role-based-access-control/role-assignments-portal.md) 

- [如何使用 Azure AD 标识和访问评审](../active-directory/governance/access-reviews-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](/security/benchmarks/security-controls-v2-data-protection)。

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4：加密传输中的敏感信息

**指导**：默认情况下，Azure 数据库迁移服务使用 TLS 1.2 或更高版本对从客户配置的源传输到数据库迁移服务实例的传输中数据进行加密。 如果源服务器不支持 TLS 1.2 连接，你可以选择禁用此功能，但强烈建议不要这样做。 从数据库迁移服务实例到目标实例的数据传输始终加密。  

在 Azure 数据库迁移服务外部，你可以使用访问控制，并应对传输中的数据使用加密技术防止“带外”攻击（例如流量捕获），以确保攻击者无法轻松读取或修改数据。 对于 HTTP 流量，请确保连接到 Azure 资源的任何客户端都能协商 TLS v1.2 或更高版本。
对于远程管理，请使用 SSH（适用于 Linux）或 RDP/TLS（适用于 Windows），而不是使用未加密的协议。 应当禁用已过时的 SSL/TLS/SSH 版本、协议以及弱密码。

在底层基础结构中，Azure 默认情况下为 Azure 数据中心之间的数据流量提供传输中数据加密。

- [了解 Azure 传输中的加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [传输中的 Azure 数据的双重加密](../security/fundamentals/double-encryption.md#data-in-transit)

**Azure 安全中心监视**：目前不可用

**责任**：共享

## <a name="asset-management"></a>资产管理

有关详细信息，请参阅 [Azure 安全基准：资产管理](/security/benchmarks/security-controls-v2-asset-management)。

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1：确保安全团队可以了解与资产相关的风险

**指南**：确保在 Azure 租户和订阅中向安全团队授予了安全读取者权限，以便他们可以使用 Azure 安全中心监视安全风险。 

根据安全团队责任划分方式的不同，监视安全风险可能是中心安全团队或本地团队的责任。 也就是说，安全见解和风险必须始终在组织内集中聚合。 

安全读取者权限可以广泛应用于整个租户（根管理组），也可以限制到管理组或特定订阅。 

若要了解工作负载和服务，可能需要更多权限。 

- [安全读取者角色概述](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理组概述](../governance/management-groups/overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2：确保安全团队有权访问资产清单和元数据

**指导**：将标记应用到 Azure 资源、资源组和订阅，以便有条理地将它们组织成分类。 每个标记均由名称和值对组成。 例如，可以对生产中的所有资源应用名称“Environment”和值“Production”。

Azure 数据库迁移服务不允许在其资源上运行应用程序或安装软件。 

- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md) 

- [Azure 安全中心资产库存管理](../security-center/asset-inventory.md) 

- [资源命名和标记决策指南](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="am-3-use-only-approved-azure-services"></a>AM-3：仅使用已批准的 Azure 服务

**指导**：请使用 Azure Policy 来审核和限制用户可以在你的环境中预配哪些服务。 使用 Azure Resource Graph 查询和发现订阅中的资源。 你也可以使用 Azure Monitor 来创建规则，以便在检测到未经批准的服务时触发警报。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure Policy 拒绝特定的资源类型](../governance/policy/samples/built-in-policies.md#general) 

- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5：限制用户与 Azure 资源管理器进行交互的能力

**指导**：通过为“Microsoft Azure 管理”应用配置“阻止访问”，使用 Azure 条件访问来限制用户与 Azure 资源管理器交互的能力。

- [如何配置条件访问来阻止对 Azure 资源管理器的访问](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="logging-and-threat-detection"></a>日志记录和威胁检测

有关详细信息，请参阅 [Azure 安全基准：日志记录和威胁检测](/security/benchmarks/security-controls-v2-logging-threat-detection)。

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2：启用 Azure 标识和访问管理的威胁检测

**指导**：Azure AD 提供以下用户日志，你可以在 Azure AD 报表中查看这些日志，也可将其与 Azure Monitor 或其他 SIEM/监视工具集成，这适用于更复杂的监视和分析用例：

登录 - 登录报告提供有关托管应用程序的使用情况和用户登录活动的信息。

审核日志 - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如添加或删除用户、应用、组、角色和策略）所做的更改。


- [Azure Active Directory 中的“审核活动”报表](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure 安全中心的威胁防护](/security-center/threat-protection)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3：为 Azure 网络活动启用日志记录

**指导**：启用并收集网络安全组 (NSG) 资源日志、NSG 流日志和 Azure 防火墙日志进行安全分析，为事件调查、威胁搜寻和安全警报生成提供支持。 可将流日志发送到 Azure Monitor Log Analytics 工作区，然后使用流量分析提供见解。

Azure 数据库迁移服务不会生成或处理那些需要启用的 DNS 查询日志。

- [如何启用网络安全组流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Azure 防火墙日志和指标](../firewall/logs-and-metrics.md) 

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md) 

- [使用网络观察程序进行监视](../network-watcher/network-watcher-monitoring-overview.md) 

**Azure 安全中心监视**：是

**责任**：客户

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：集中管理和分析安全日志

**指导**：集中记录存储和分析来实现关联。 对于每个日志源，请确保已分配数据所有者、访问指南、存储位置、用于处理和访问数据的工具以及数据保留要求。

确保正在将 Azure 活动日志集成到中央日志记录。 通过 Azure Monitor 引入日志，以聚合终结点设备、网络资源和其他安全系统生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期存档存储。

此外，启用数据并将其加入第三方 SIEM。

许多组织选择将 Azure Sentinel 用于频繁使用的“热”数据，并将 Azure 存储用于不太频繁使用的“冷”数据。

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md) 

**Azure 安全中心监视**：是

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](/security/benchmarks/security-controls-v2-incident-response)。

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：准备 - 更新 Azure 的事件响应流程

**指导**：确保组织具有响应安全事件的流程，已为 Azure 更新这些流程，并定期运用这些流程来确保就绪性。

- [在企业环境中实现安全性](https://docs.microsoft.com/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) 

- [事件响应参考指南](https://docs.microsoft.com/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2：准备 - 设置事件通知

**指导**：在 Azure 安全中心中设置安全事件联系人信息。 如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的一方访问了你的数据，Azure 将使用此联系信息来与你联系。 还可以选择基于事件响应需求在不同的 Azure 服务中自定义事件警报和通知。 

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：检测和分析 - 基于高质量警报创建事件

**指导**：确保具有创建高质量警报和衡量警报质量的流程。 这样，你就可以从过去的事件中吸取经验，并为分析人员确定警报的优先级，确保他们不会浪费时间来处理误报。 

可以基于过去的事件经验、经验证的社区源以及旨在通过融合和关联各种信号源来生成和清理警报的工具构建高质量警报。 

Azure 安全中心可跨许多 Azure 资产提供高质量的警报。 

使用导出功能导出 Azure 安全中心警报和建议，以帮助识别 Azure 资源的风险。 手动导出或持续导出警报和建议。

- [如何配置导出](../security-center/continuous-export.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4：检测和分析 - 调查事件

**指南**：确保分析人员在调查潜在事件时可查询和使用不同的数据源，以全面了解所发生的情况。 应收集各种各样的日志，以跟踪整个终止链中潜在攻击者的活动，避免出现盲点。  还应确保收集见解和经验，以供其他分析人员使用和用作将来的历史参考资料。  

用于调查的数据源包括已从作用域内服务和正在运行的系统中收集的集中式日志记录源，但还可以包括以下内容：

- 网络数据 - 使用网络安全组的流日志、Azure 网络观察程序和 Azure Monitor 来捕获网络流日志和其他分析信息。 

- 正在运行的系统的快照： 

    - 使用 Azure 虚拟机的快照功能创建正在运行的系统磁盘的快照。 

    - 使用操作系统的本机内存转储功能来创建正在运行的系统内存的快照。

    - 使用 Azure 服务的快照功能或软件自带的功能来创建正在运行的系统的快照。

- [Windows 计算机的磁盘快照](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux 计算机的磁盘快照](../virtual-machines/linux/snapshot-copy-managed-disk.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5：检测和分析 - 确定事件优先级

**指南**：根据警报严重性和资产敏感度，为分析人员提供上下文来确定应首要关注哪些事件。 

Azure 安全中心为每条警报分配严重性，方便你根据优先级来确定应该最先调查的警报。 严重性取决于安全中心对调查结果或用于发出警报的分析的确信程度，以及对导致警报的活动背后存在恶意意图的确信程度。

此外，使用标记来标记资源，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)

- [使用标记整理 Azure 资源](/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：包含、根除和恢复 - 自动执行事件处理

**指导**：自动执行手动重复性任务来加快响应时间并减轻分析人员的负担。 执行手动任务需要更长的时间，这会导致减慢每个事件的速度，并减少分析人员可以处理的事件数量。 手动任务还会使分析人员更加疲劳，这会增加可导致延迟的人为错误的风险，并降低分析人员专注于复杂任务的工作效率。 使用 Azure 安全中心的工作流自动化功能，可自动触发操作或运行 playbook，对传入的安全警报作出响应。 playbook 执行多项操作，如发送通知、禁用帐户和隔离有问题的网络。 

- [在安全中心配置工作流自动化](../security-center/workflow-automation.md)

- [在 Azure 安全中心设置自动威胁响应](../security-center/tutorial-security-incident.md#triage-security-alerts)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="posture-and-vulnerability-management"></a>安全状况和漏洞管理

有关详细信息，请参阅 [Azure 安全基准：安全状况和漏洞管理](/security/benchmarks/security-controls-v2-posture-vulnerability-management)。

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8：执行定期攻击模拟

**指导**：根据需要，对 Azure 资源进行渗透测试或红队活动，并确保修正所有关键安全发现。
请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Azure 政策。 使用 Azure 推出的红队演练策略和执行方式，以及针对 Microsoft 管理的云基础结构、服务和应用程序进行的现场渗透测试。

- [Azure 中的渗透测试](../security/fundamentals/pen-testing.md)

- [参与的渗透测试规则](https://www.trustcenter.cn/zh-cn/security/penetrationtesting.html) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="governance-and-strategy"></a>治理和策略

有关详细信息，请参阅 [Azure 安全基准：治理和策略](/security/benchmarks/security-controls-v2-governance-strategy)。

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1：定义资产管理和数据保护策略 

**指导**：确保为系统和数据的持续监视和保护记录并传达明确的策略。 确定业务关键数据和系统的发现、评估、保护和监视优先级。 

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

- 与业务风险相符的数据分类标准

- 安全组织对风险和资产清单的洞察力 

- 安全组织对 Azure 服务使用的审批 

- 资产在其生命周期中的安全性

- 与组织数据分类相符的必需访问控制策略

- 使用 Azure 原生和第三方数据保护功能

- 传输中数据用例和静态数据用例的数据加密要求

- 合适的加密标准

有关详细信息，请参阅以下资源：
- [Azure 安全体系结构建议 - 存储、数据和加密](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure 安全基础知识 - Azure 数据安全、加密和存储](../security/fundamentals/encryption-overview.md)

- [云采用框架 - Azure 数据安全和加密最佳做法](/security/fundamentals/data-encryption-best-practices?toc=/cloud-adoption-framework/toc.json&amp;bc=/cloud-adoption-framework/_bread/toc.json)

- [Azure 安全基准 - 资产管理](/security/benchmarks/security-controls-v2-asset-management)

- [Azure 安全基准 - 数据保护](/security/benchmarks/security-controls-v2-data-protection)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2：定义企业分段策略 

**指导**：建立企业范围的策略，以便使用标识、网络、应用程序、订阅、管理组和其他控件的组合来细分对资产的访问。

仔细权衡安全分离需求与为需要彼此通信并访问数据的系统启用日常操作的需求。

确保跨控制类型（包括网络安全、标识和访问模型、应用程序权限/访问模型，以及人机过程控制）一致地实现分段策略。

- [有关 Azure 中的分段策略的指南（文档）](https://docs.microsoft.com/security/compass/governance#enterprise-segmentation-strategy)

- [使网络分段与企业分段策略相匹配](https://docs.microsoft.com/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3：定义安全状况管理策略

**指导**：持续衡量并缓解你的个人资产及其托管环境的风险。 确定高价值资产和暴露程度高的受攻击面（例如已发布的应用程序、网络入口和出口点、用户和管理员终结点等）的优先级。

- [Azure 安全基准 - 状况和漏洞管理](/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4：协调组织角色、职责和责任

**指导**：确保为安全组织中的角色和责任记录并传达明确的策略。 优先考虑提供涉及安全决策的明确责任，对每个人进行共同职责模式培训，并为技术团队传授保护云的技术。

- [Azure 安全最佳做法 1 - 人员：针对云安全历程培训团队](https://docs.microsoft.com/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure 安全最佳做法 2 - 人员：针对云安全技术培训团队](https://docs.microsoft.com/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure 安全最佳做法 3 - 流程：针对云安全决策分配责任](https://docs.microsoft.com/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure 安全中心监视**：不适用

**责任**：客户

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
- [Azure 安全最佳做法 11 - 体系结构。单一的统一安全策略](https://docs.microsoft.com/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 安全基准 - 网络安全](/security/benchmarks/security-controls-v2-network-security)

- [Azure 网络安全概述](../security/fundamentals/network-overview.md)

- [企业网络体系结构策略](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

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

- [Azure 安全基准 - 标识管理](/security/benchmarks/security-controls-v2-identity-management)

- [Azure 安全基准 - 特权访问](/security/benchmarks/security-controls-v2-privileged-access)

- [Azure 安全最佳做法 11 - 体系结构。单一的统一安全策略](https://docs.microsoft.com/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 标识管理安全概述](../security/fundamentals/identity-management-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7：定义日志记录和威胁响应策略

**指导**：建立日志记录和威胁响应策略，以快速检测和修正威胁，同时满足合规性要求。 优先为分析人员提供高质量警报和无缝体验，以便他们能够专注于威胁而不是集成和手动步骤。 

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

- 安全运营 (SecOps) 组织的角色和职责 

- 符合 NIST 或其他行业框架要求的明确定义的事件响应流程 

- 日志捕获和保留，用于支持威胁检测、事件响应和合规性需求

- 使用 SIEM、原生 Azure 功能和其他源，集中查看和关联有关威胁的信息 

- 与客户、供应商和公开的利益相关方之间的通信和通知计划

- 使用 Azure 原生的和第三方的平台进行事件处理，例如日志记录和威胁检测、取证以及攻击补救和根除

- 处理事件和事件后活动的流程，例如经验教训和证据保留

有关详细信息，请参阅以下资源：

- [Azure 安全基准 - 日志记录和威胁检测](/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure 安全基准 - 事件响应](/security/benchmarks/security-controls-v2-incident-response)

- [Azure 安全最佳做法 4 - 流程。更新云的事件响应流程](https://docs.microsoft.com/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 采用框架、日志记录和报告决策指南](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 企业规模、管理和监视](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/security/benchmarks/security-baselines-overview)
