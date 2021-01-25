---
title: 适用于 Azure 数据资源管理器的 Azure 安全基线
description: Azure 数据资源管理器安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: data-explorer
ms.topic: conceptual
origin.date: 11/24/2020
ms.date: 01/19/2021
ms.author: v-tawe
ms.custom: subject-security-benchmark
ms.openlocfilehash: 3a7a8885a6245bfdd4280d218451e008484b36aa
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611661"
---
# <a name="azure-security-baseline-for-azure-data-explorer"></a>适用于 Azure 数据资源管理器的 Azure 安全基线

此安全基线将 [Azure 安全基准版本 1.0](https://docs.azure.cn/security/benchmarks/overview-v1) 中的指南应用于 Azure 数据资源管理器。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。
内容按“安全控制”分组，这些控制根据适用于 Azure 数据资源管理器的 Azure 安全基准和相关指南定义。 排除了不适用于 Azure 数据资源管理器的“控制”。

 
若要查看 Azure 数据资源管理器如何完全映射到 Azure 安全基准，请参阅[完整的 Azure 数据资源管理器安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](https://docs.azure.cn/security/benchmarks/security-control-network-security)。

<!--
### 1.1: Protect Azure resources within virtual networks

**Guidance**: Azure Data Explorer supports deploying a cluster into a subnet in your virtual network. This capability enables you to enforce network security group (NSG) rules on your Azure Data Explorer cluster traffic, connect your on-premises network to Azure Data Explorer cluster's subnet, and Secure your data connection sources (Event Hub and Event Grid) with service endpoints.

- [How to create a cluster in your Virtual Network](https://docs.azure.cn/data-explorer/vnet-create-cluster-portal)

- [How to deploy your Azure Data Explorer cluster into a Virtual Network](https://docs.azure.cn/data-explorer/vnet-deployment)

- [How to troubleshoot your Virtual Network cluster creation, connectivity, and operation](https://docs.azure.cn/data-explorer/vnet-deploy-troubleshoot?tabs=windows)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer
-->

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：监视并记录虚拟网络、子网和网络接口的配置与流量

**指导**：启用网络安全组 (NSG) 流日志，并将日志发送到存储帐户以进行流量审核。

如何启用 NSG 流日志： https://docs.azure.cn/network-watcher/network-watcher-nsg-flow-logging-portal

了解 Azure 安全中心提供的网络安全： https://docs.azure.cn/security-center/security-center-network-recommendations

**Azure 安全中心监视**：是

**责任**：客户

<!--
### 1.4: Deny communications with known-malicious IP addresses

**Guidance**: Enable Azure DDoS Protection Standard on the virtual network protecting your Azure Data Explorer clusters for protection against DDoS attacks. Use Azure Security Center Integrated Threat Intelligence to deny communications with known malicious or unused Internet IP addresses.

- [How to configure DDoS protection](https://docs.azure.cn/virtual-network/manage-ddos-protection)

- [Understand Azure Security Center Integrated Threat Intelligence](https://docs.azure.cn/security-center/security-center-alerts-service-layer)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer
-->

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指导**：在用于保护 Azure 数据资源管理器群集的网络安全组 (NSG) 上启用流日志，并将日志发送到存储帐户以进行流量审核。

如何启用 NSG 流日志： https://docs.azure.cn/network-watcher/network-watcher-nsg-flow-logging-portal

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：在与 Azure 数据资源管理器群集关联的网络安全组或 Azure 防火墙中使用虚拟网络服务标记来定义网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

- [了解并使用服务标记](https://docs.azure.cn/virtual-network/service-tags-overview)

- [Azure 数据资源管理器的“服务标签”配置要求](https://docs.azure.cn/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)

**Azure 安全中心监视**：目前不可用

**责任**：客户 -->

<!--
### 1.9: Maintain standard security configurations for network devices

**Guidance**: Customer to define and implement standard security configurations for network resources with Azure Policy.

Customer may also use Azure Blueprints to simplify large scale Azure deployments by packaging key environment artifacts, such as Azure Resource Manager templates, Azure RBAC controls, and Azure Policy assignments, in a single blueprint definition. Easily apply the blueprint to new subscriptions and environments, and fine-tune control and management through versioning.

- [How to configure and manage Azure Policy](https://docs.azure.cn/governance/policy/tutorials/create-and-manage)

- [How to create an Azure Blueprint](https://docs.azure.cn/governance/blueprints/create-blueprint-portal)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer
-->

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：将标记用于网络安全组 (NSG) 以及与 Azure 数据资源管理器群集的网络安全和通信流相关的其他资源。 对于单个 NSG 规则，请使用“说明”字段针对允许流量传入/传出网络的任何规则指定业务需求和/或持续时间等。

- [如何创建和使用标记](https://docs.azure.cn/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure Policy 来验证（和/或修正）网络资源的配置。

- [如何配置和管理 Azure Policy](https://docs.azure.cn/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](https://docs.azure.cn/security/benchmarks/security-control-logging-monitoring)。

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：Azure 数据资源管理器使用诊断日志获取有关引入成功和失败的见解。 可将操作日志导出到 Azure 存储、事件中心或 Log Analytics 以监视引入状态。

- [如何监视 Azure 数据资源管理器引入操作](https://docs.azure.cn/data-explorer/using-diagnostic-logs)

- [如何在 Azure 数据资源管理器中引入和查询监视数据](https://docs.azure.cn/data-explorer/ingest-data-no-code)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：启用 Azure 数据资源管理器的诊断设置以访问和记录服务专用操作和日志记录。 默认情况下，Azure Monitor 中的 Azure 活动日志（包括有关资源的概要日志记录）处于启用状态。

- [如何监视 Azure 数据资源管理器引入操作](https://docs.azure.cn/data-explorer/using-diagnostic-logs)

- [如何使用 Azure Monitor 收集平台日志和指标](https://docs.azure.cn/azure-monitor/platform/diagnostic-settings)

- [Azure 平台日志概述](https://docs.azure.cn/azure-monitor/platform/platform-logs-overview)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：在 Azure Monitor 中，根据组织的合规性规章设置 Log Analytics 工作区保留期。 使用 Azure 存储帐户进行长期/存档存储。

- [如何为 Log Analytics 工作区设置日志保留参数](https://docs.azure.cn/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指导**：分析和监视日志中的异常行为，并定期审查结果。 启用 Azure 数据资源管理器的诊断设置后，使用 Azure Monitor 的 Log Analytics 工作区查看日志并对日志数据执行查询。

- [了解 Log Analytics 工作区](https://docs.azure.cn/azure-monitor/log-query/get-started-portal)

- [如何在 Azure Monitor 中执行自定义查询](https://docs.azure.cn/azure-monitor/log-query/get-started-queries)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](https://docs.azure.cn/security/benchmarks/security-control-identity-access-control)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：在 Azure 数据资源管理器中，安全角色定义哪些安全主体（用户和应用程序）有权对受保护的资源（例如数据库或表）进行操作，以及允许进行哪些操作。 可以利用 Kusto 查询列出 Azure 数据资源管理器群集和数据库的具有管理员角色的主体。

- [使用 Kusto 查询在 Azure 数据资源管理器中进行安全角色管理](https://docs.azure.cn/kusto/management/security-roles)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：Azure Active Directory (Azure AD) 没有默认密码的概念。 其他需要密码的 Azure 资源会强制创建具有复杂性要求和最小密码长度的密码，该长度因服务而异。 你对可能使用默认密码的第三方应用程序和市场服务负责。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指导**：客户围绕专用管理帐户的使用创建标准操作程序。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。

客户还可以通过使用 Microsoft 服务的 Azure Active Directory (Azure AD) Privileged Identity Management 特权角色和 Azure ARM 来启用实时/恰好足够的访问权限。 

- [Privileged Identity Management](https://docs.azure.cn/active-directory/privileged-identity-management/pim-configure)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 单一登录 (SSO)

**指导**：客户应尽可能使用 Azure Active Directory (Azure AD) SSO，而不是为每个服务配置单个独立凭据。 请使用 Azure 安全中心标识和访问管理建议。

<!-- Understanding SSO with Azure AD:  https://docs.azure.cn/active-directory/manage-apps/what-is-single-sign-on -->

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指导**：启用 Azure Active Directory (Azure AD) 多重身份验证 (MFA)，并遵循 Azure 安全中心标识和访问管理的建议。

- [如何在 Azure 中启用 MFA](https://docs.azure.cn/active-directory/authentication/howto-mfa-getstarted)

- [如何在 Azure 安全中心监视标识和访问](https://docs.azure.cn/security-center/security-center-identity-access)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用由 Azure 管理的安全工作站执行管理任务

**指导**：使用配置了多重身份验证 (MFA) 的 PAW（特权访问工作站）来登录和配置 Azure 资源。

- [了解特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [如何在 Azure 中启用 MFA](https://docs.azure.cn/active-directory/authentication/howto-mfa-getstarted)

**Azure 安全中心监视**：不适用

**责任**：客户

<!--
### 3.7: Log and alert on suspicious activities from administrative accounts

**Guidance**: Use Azure Active Directory (Azure AD) security reports for generation of logs and alerts when suspicious or unsafe activity occurs in the environment. Use Azure Security Center to monitor identity and access activity

- [How to identify Azure AD users flagged for risky activity](https://docs.azure.cn/active-directory/reports-monitoring/concept-user-at-risk)

- [How to monitor users identity and access activity in Azure Security Center](https://docs.azure.cn/security-center/security-center-identity-access)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer
-->

<!--
### 3.8: Manage Azure resources from only approved locations

**Guidance**: Customer to use Conditional Access named locations to allow access from only specific logical groupings of IP address ranges or countries/regions.

- [How to configure named locations in Azure](https://docs.azure.cn/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer
-->

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：Azure Active Directory (Azure AD) 是向 Azure 数据资源管理器进行身份验证的首选方法。 Azure AD 支持多种身份验证方案：

- 用户身份验证（交互式登录）：用于对人类主体进行身份验证。

- 应用程序身份验证（非交互式登录）：用于对必须在没有人类用户参与的情况下运行或进行身份验证的服务和应用程序进行身份验证。

有关详细信息，请参阅以下资源：

- [Azure 数据资源管理器访问控制概述](https://docs.azure.cn/kusto/management/access-control)

- [使用 Azure Active Directory 进行身份验证](https://docs.azure.cn/kusto/management/access-control/aad)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Active Directory (Azure AD) 提供日志来帮助发现过时的帐户。 此外，请使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。 

- [如何使用 Azure AD 进行 Azure 数据资源管理器访问身份验证](https://docs.azure.cn/kusto/management/access-control/how-to-authenticate-with-aad)

- [Azure AD 报告](https://docs.azure.cn/active-directory/reports-monitoring/)

- [如何使用 Azure 标识访问评审](https://docs.azure.cn/active-directory/governance/access-reviews-overview)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指导**：可使用 Azure Active Directory (Azure AD) 登录活动、审核和风险事件日志源进行监视，这让你可以与任何安全信息和事件管理 (SIEM)/监视工具集成。

 你可以通过为 Azure Active Directory 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 客户可以在 Log Analytics 工作区中配置所需的警报。

<!-- How to integrate Azure Activity Logs into Azure Monitor:  https://docs.azure.cn/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics -->

**Azure 安全中心监视**：目前不可用

**责任**：客户

<!--
### 3.12: Alert on account sign-in behavior deviation

**Guidance**: Use Azure Active Directory (Azure AD) Risk Detections and Identity Protection feature to configure automated responses to detected suspicious actions related to user identities. Additionally, you can ingest data into Azure Sentinel for further investigation.

- [How to view Azure AD risky sign-ins](https://docs.azure.cn/active-directory/reports-monitoring/concept-risky-sign-ins)

- [How to configure and enable Identity Protection risk policies](https://docs.azure.cn/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer
-->

<!--
### 3.13: Provide Microsoft with access to relevant customer data during support scenarios

**Guidance**: In support scenarios where Microsoft needs to access customer data, Customer Lockbox provides an interface for customers to review and approve or reject customer data access requests.

- [Understanding Customer Lockbox](https://docs.azure.cn/security/fundamentals/customer-lockbox-overview)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer
-->

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](https://docs.azure.cn/security/benchmarks/security-control-data-protection)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记可以帮助跟踪存储或处理敏感信息的 Azure 资源。

- [如何创建和使用标记](https://docs.azure.cn/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：目前不可用

**责任**：客户

<!--
### 4.2: Isolate systems storing or processing sensitive information

**Guidance**: Implement separate subscriptions and/or management groups for development, test, and production. Azure Data Explorer clusters should be separated from other resources by virtual network/subnet, tagged appropriately, and secured within an network security group (NSG) or Azure Firewall. Azure Data Explorer clusters storing or processing sensitive data should be sufficiently isolated.

- [How to create additional Azure subscriptions](https://docs.azure.cn/billing/billing-create-subscription)

- [How to create Management Groups](https://docs.azure.cn/governance/management-groups/create)

- [How to create and use tags](https://docs.azure.cn/azure-resource-manager/resource-group-using-tags)

- [How to deploy your Azure Data Explorer cluster into a virtual network](https://docs.azure.cn/data-explorer/vnet-deployment)

- [How to create an NSG with a Security Config](https://docs.azure.cn/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer
-->

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：默认情况下，Azure 数据资源管理器群集协商 TLS 1.2。 确保连接到 Azure 资源的任何客户端能够协商 TLS 1.2 或更高版本。

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：数据标识、分类和丢失防护功能尚不适用于 Azure 数据资源管理器。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [了解 Azure 中的客户数据保护](https://docs.azure.cn/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

**指导**：通过 Azure 数据资源管理器，可以使用 Azure 基于角色的访问控制 (RBAC) 模型来控制对数据库和表的访问。 在此模型下，主体（用户、组和应用）将映射到角色。 主体可以根据分配的角色访问资源。

- [角色和权限列表以及有关如何为 Azure 数据资源管理器配置 RBAC 的说明](https://docs.azure.cn/data-explorer/manage-database-permissions)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指导**：Azure 磁盘加密有助于保护数据，使组织能够信守在安全性与合规性方面作出的承诺。 它为群集虚拟机的 OS 和数据磁盘提供卷加密。 它还与 Azure 密钥保管库集成，让我们可以控制和管理磁盘加密密钥和机密，并确保 VM 磁盘上的所有数据在 Azure 存储中进行静态加密。

- [如何为 Azure 数据资源管理器群集启用静态加密](https://docs.azure.cn/data-explorer/manage-cluster-security)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：将 Azure Monitor 与 Azure 活动日志配合使用，以创建在 Azure 数据资源管理器上发生资源级别更改时发出的警报。

- [如何针对 Azure 活动日志事件创建警报](https://docs.azure.cn/azure-monitor/platform/alerts-activity-log)

- [如何针对 Azure 活动日志事件创建警报](https://docs.azure.cn/azure-monitor/platform/alerts-activity-log)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

[有关详细信息，请参阅 *Azure 安全基线：* 漏洞管理。](https://docs.azure.cn/security/benchmarks/security-control-vulnerability-management)

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指导**：使用 Azure 安全中心提供的默认风险评级（安全功能分数）。

- [了解 Azure 安全中心安全功能分数](https://docs.azure.cn/security-center/security-center-secure-score)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="inventory-and-asset-management"></a>清单和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](https://docs.azure.cn/security/benchmarks/security-control-inventory-asset-management)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 查询和发现订阅中的所有资源。 确保租户中具有适当的（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器资源。

- [如何使用 Azure Resource Graph 创建查询](https://docs.azure.cn/governance/resource-graph/first-query-portal)

- [如何查看 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [了解 Azure RBAC](https://docs.azure.cn/role-based-access-control/overview)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。

- [如何创建和使用标记](https://docs.azure.cn/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：在适用的情况下，你可以使用适当的命名约定、标记、管理组或单独的订阅来组织和跟踪资产。 你可以使用 Azure Resource Graph 定期核对清单，确保及时地从订阅中删除未经授权的资源。 

<!-- - [How to create additional Azure subscriptions](https://docs.azure.cn/billing/billing-create-subscription) -->

- [如何创建管理组](https://docs.azure.cn/governance/management-groups/create)

- [如何创建和使用标记](https://docs.azure.cn/azure-resource-manager/resource-group-using-tags)

- [如何使用 Azure Resource Graph 创建查询](https://docs.azure.cn/governance/resource-graph/first-query-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准的 Azure 资源的清单

**指导**：你将需要根据组织需求，创建已获批 Azure 资源以及已获批用于计算资源的软件的清单。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：可以在 Azure 策略中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

你将能够使用活动日志（可使用 Azure Monitor 对其进行监视）监视策略生成的事件。

此外，可以使用 Azure Resource Graph 来查询/发现订阅中的资源。

- [创建和管理策略以强制实施符合性](https://docs.azure.cn/governance/policy/tutorials/create-and-manage)

- [快速入门：使用 Azure Resource Graph 资源管理器运行你的第一个 Resource Graph 查询](https://docs.azure.cn/governance/resource-graph/first-query-portal)

- [使用 Azure Monitor 创建、查看和管理活动日志警报](https://docs.azure.cn/azure-monitor/platform/alerts-activity-log)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：可以在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

 - 不允许的资源类型

 - 允许的资源类型

有关详细信息，请参阅以下资源：

- [创建和管理策略以强制实施符合性](https://docs.azure.cn/governance/policy/tutorials/create-and-manage)

- [Azure Policy 示例](https://docs.azure.cn/governance/policy/samples/built-in-policies#general)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

指南：使用 Azure 条件访问可通过为“Microsoft Azure 管理”应用配置“阻止访问”，来限制用户与 Azure 资源管理器进行交互的能力。 这会阻止在 Azure 订阅中创建和更改资源。 

- [使用条件访问管理对 Azure 管理的访问权限](https://docs.azure.cn/role-based-access-control/conditional-access-azure-management)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](https://docs.azure.cn/security/benchmarks/security-control-secure-configuration)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：使用 Azure Policy 别名创建自定义策略，审核或强制实施 Azure 资源的配置。 你还可以使用内置的 Azure Policy 定义。

此外，Azure 资源管理器能够以 JavaScript 对象表示法 (JSON) 导出模板，应该对其进行检查，以确保配置满足或超过组织的安全要求。

还可以使用来自 Azure 安全中心的建议作为 Azure 资源的安全配置基线。

- [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [教程：创建和管理策略以强制实施符合性](https://docs.azure.cn/governance/policy/tutorials/create-and-manage)

- [在 Azure 门户中将单资源和多资源导出到模板](https://docs.azure.cn/azure-resource-manager/templates/export-template-portal)

- [安全建议 - 参考指南](https://docs.azure.cn/security-center/recommendations-reference)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 Azure 策略“[拒绝]”和“[不存在则部署]”对不同的 Azure 资源强制实施安全设置。  可以使用更改跟踪、策略符合性仪表板等解决方案或自定义解决方案来轻松识别环境中的安全更改。

- [了解 Azure Policy 效果](https://docs.azure.cn/governance/policy/concepts/effects)

- [创建和管理策略以强制实施符合性](https://docs.azure.cn/governance/policy/tutorials/create-and-manage)

- [使用更改跟踪解决方案跟踪环境中的更改](https://docs.azure.cn/automation/change-tracking)

- [获取 Azure 资源的符合性数据](https://docs.azure.cn/governance/policy/how-to/get-compliance-data)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指导**：使用 Azure Repos 安全地存储和管理代码，如自定义 Azure 策略、Azure 资源管理器模板、Desired State Configuration 脚本等。若要访问在 Azure DevOps 中管理的资源，可以向特定用户、内置安全组或 Azure Active Directory (Azure AD)（如果与 Azure DevOps 集成）中定义的组或 Active Directory（如果与 TFS 集成）授予或拒绝授予权限。

- [如何在 Azure DevOps 中存储代码](https://docs.azure.cn/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [关于 Azure DevOps 中的权限和组](https://docs.azure.cn/devops/organizations/security/about-permissions)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：使用 Azure Policy 为 Azure 资源定义和实施标准安全配置。 使用 Azure Policy 别名创建自定义策略，审核或强制实施 Azure 资源的网络配置。 还可以使用与特定资源相关的内置策略定义。  此外，你也可以使用 Azure 自动化来部署配置更改。

- [如何配置和管理 Azure Policy](https://docs.azure.cn/governance/policy/tutorials/create-and-manage)

- [如何使用别名](https://docs.azure.cn/governance/policy/concepts/definition-structure#aliases)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：使用 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并为其发出警报。 使用 Azure Policy“[审核]”、“[拒绝]”和“[不存在则部署]”自动强制实施 Azure 资源的配置。

- [如何配置和管理 Azure Policy](https://docs.azure.cn/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指导**：使用托管标识在 Azure Active Directory (Azure AD) 中为 Azure 服务提供一个自动托管的标识。 使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Key Vault）进行身份验证，无需在代码中放入任何凭据。

- [如何配置托管标识](https://docs.azure.cn/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

- [配置 Azure 数据资源管理器群集的托管标识](https://docs.azure.cn/data-explorer/managed-identities)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。 

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

[有关详细信息，请参阅 *Azure 安全基线：* 恶意软件防护](https://docs.azure.cn/security/benchmarks/security-control-malware-defense)。

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指导**：在支持 Azure 服务（例如 Azure 数据资源管理器）的底层主机上已启用 Microsoft Antimalware，但是，该软件不会针对客户内容运行。

预扫描要上传到非计算 Azure 资源的任何内容，例如 Azure 数据资源管理器、Data Lake Storage、Blob 存储、Azure Database for PostgreSQL 等。Microsoft 无法访问这些实例中的数据。

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](https://docs.azure.cn/security/benchmarks/security-control-data-recovery)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指导**：始终复制 Microsoft Azure 存储帐户中 Azure 数据资源管理器群集使用的数据，以确保持续性和高可用性。 Azure 存储功能会复制数据，以防范各种计划内和计划外的事件，包括暂时性的硬件故障、网络中断或断电、大范围自然灾害等。 可以选择在同一数据中心中、跨同一区域中的局域数据中心或跨地理上隔离的区域复制数据。

- [了解 Azure 存储冗余和服务级别协议](https://docs.azure.cn/storage/common/storage-redundancy)

- [将数据导出到存储](https://docs.azure.cn/kusto/management/data-export/export-data-to-storage)

**Azure 安全中心监视**：目前不可用

**责任**：客户

<!--
### 9.2: Perform complete system backups and backup any customer-managed keys

**Guidance**: Azure Data Explorer encrypts all data in a storage account at rest. By default, data is encrypted with Microsoft-managed keys. For additional control over encryption keys, you can supply customer-managed keys to use for data encryption. Customer-managed keys must be stored in an Azure Key Vault.

- [Configure customer-managed-keys using the Azure portal ](https://docs.azure.cn/data-explorer/customer-managed-keys-portal)

- [Configure customer-managed-keys using C# ](https://docs.azure.cn/data-explorer/customer-managed-keys-csharp)

- [Configure customer-managed-keys using the Azure Resource Manager template](https://docs.azure.cn/data-explorer/customer-managed-keys-resource-manager)

- [How to backup Azure Key Vault certificates](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-4.8.0&amp;preserve-view=true)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### 9.3: Validate all backups including customer-managed keys

**Guidance**: Periodically test data restoration of your Azure Key Vault secrets.

- [How to restore Azure Key Vault certificates](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultcertificate?view=azps-4.8.0&amp;preserve-view=true)

- [Configure customer-managed-keys using C# ](https://docs.azure.cn/data-explorer/customer-managed-keys-csharp)

- [Configure customer-managed-keys using the Azure Resource Manager template](https://docs.azure.cn/data-explorer/customer-managed-keys-resource-manager)

**Azure Security Center monitoring**: Not Applicable

**Responsibility**: Customer

### 9.4: Ensure protection of backups and customer-managed keys

**Guidance**: Customer to enable Soft-Delete in Key Vault to protect keys against accidental or malicious deletion.  You can also enable purge protection so that if a vault or an object in the deleted state, it cannot be purged until the retention period has passed.  

- [How to enable Soft-Delete and Purge protection in Key Vault](https://docs.azure.cn/key-vault/key-vault-ovw-soft-delete)

- [Configure customer-managed-keys using C# ](https://docs.azure.cn/data-explorer/customer-managed-keys-csharp)

- [Configure customer-managed-keys using the Azure Resource Manager template](https://docs.azure.cn/data-explorer/customer-managed-keys-resource-manager)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer
-->

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](https://docs.azure.cn/security/benchmarks/security-control-incident-response)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。
    

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

    

- [Microsoft 安全响应中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

    

- [客户还可利用 NIST 的“计算机安全事件处理指南”来帮助创建自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：安全中心为每条警报分配严重性，以帮助你优先处理应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，请用标记明确标记订阅（例如 生产、非生产）并创建命名系统来对 Azure 资源进行明确标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](https://docs.azure.cn/security-center/security-center-alerts-overview)

- [使用标记整理 Azure 资源](https://docs.azure.cn/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指南**：定期执行演练来测试系统的事件响应功能，以帮助保护 Azure 资源。 识别弱点和差距，并根据需要修改计划。
    

- [请参阅 NIST 的出版物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)（IT 规划和功能的测试、培训与演练计划指南）

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 事后审查事件，确保问题得到解决。
    

- [如何设置 Azure 安全中心安全联系人](https://docs.azure.cn/security-center/security-center-provide-security-contact-details)

**Azure 安全中心监视**：是

**责任**：客户

<!--
### 10.5: Incorporate security alerts into your incident response system

**Guidance**: Export your Azure Security Center alerts and recommendations using the Continuous Export feature to help identify risks to Azure resources. Continuous Export allows you to export alerts and recommendations either manually or in an ongoing, continuous fashion. You may use the Azure Security Center data connector to stream the alerts to Azure Sentinel.
    

- [ How to configure continuous export](https://docs.azure.cn/security-center/continuous-export)

    

- [ How to stream alerts into Azure Sentinel](https://docs.azure.cn/sentinel/connect-azure-security-center)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer
-->
<!--
### 10.6: Automate the response to security alerts

**Guidance**: Use the Workflow Automation feature in Azure Security Center to automatically trigger responses via "Logic Apps" on security alerts and recommendations to protect your Azure resources.
    

- [ How to configure Workflow Automation and Logic Apps](https://docs.azure.cn/security-center/workflow-automation)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer
-->

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](https://docs.azure.cn/security/benchmarks/security-control-penetration-tests-red-team-exercises)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。 

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
