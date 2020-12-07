---
title: 保护 PCI DSS 的 Web 应用 | Azure Docs
description: 这个示例应用实施了在 Azure 上进行开发时可以改进应用程序和组织的安全状况的安全最佳做法。
keywords: na
services: security
documentationcenter: na
author: fehase
manager: rkarlin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2020
origin.date: 08/22/2019
ms.author: v-tawe
ms.openlocfilehash: f848221398cf53c180823d37b07beba6f84e262a
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96432663"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>为 PCI 应用开发安全的基础结构

## <a name="overview"></a>概述

此安全的体系结构适用于支付卡行业 (PCI) 应用，提供了相关指南来引导你部署一个适合收集、存储和检索持卡人数据的支付卡行业平台即服务 (PaaS) 环境。 此解决方案自动完成适用于常用参考体系结构的 Azure 资源的部署和配置，演示了客户如何通过多种方式达到特定的安全和符合性要求，是客户在 Azure 上生成和配置其解决方案的基础。 此解决方案实现了类似于支付卡行业数据安全标准 (PCI DSS 3.2) 的一部分要求。

此示例可自动部署一个 PaaS Web 应用程序参考体系结构，该结构中预配置有安全控件，可帮助客户符合类似于 PCI DSS 3.2 的要求。 此解决方案包含 Azure 资源管理器模板和 PowerShell 脚本，指导用户进行资源部署和配置。

你应该按顺序执行本文中描述的步骤，以确保应用程序组件正确配置。 数据库、Azure 应用服务、Azure Key Vault 实例和 Azure 应用程序网关实例彼此依赖。

部署脚本会设置基础结构。 运行部署脚本后，你需要在 Azure 门户中进行一些手动配置，以将组件和服务链接在一起。

此示例适用于在 Azure 上具有丰富经验的开发人员，这些开发人员在零售行业中工作，希望使用安全的 Azure 基础结构构建零售应用。

> [!NOTE]
> 本体系结构旨在作为客户的基础，以根据他们的特定需求进行调整，而不应在生产环境中按原样使用。

未经修改就向此环境部署应用程序并不足以完全满足 PCI DSS 3.2 所设定的要求。 注意以下事项：

- 本体系结构提供一个基线，帮助客户按符合 PCI DSS 3.2 要求的方式使用 Azure。
- 客户负责针对使用本体系结构构建的任何解决方案开展相应的安全与合规评估；具体要求根据客户的每种实施方式具体情况而异。

通过开发和部署此应用，你将了解如何执行以下操作：

- 创建一个 Azure Key Vault 实例，在其中存储和检索机密。
- 部署 Azure Database for Azure SQL，设置安全的数据，并授予对数据的访问权限。
- 为 Azure Web 应用部署专用且与前端防火墙访问隔离的应用服务环境。
- 创建 Azure 应用程序网关实例并为其配置使用 [OWASP 10 大规则集](https://coreruleset.org/)的防火墙。
- 使用 Azure 服务启用传输中数据和静态数据的加密。
- 设置 Azure Policy 和 Azure 蓝图来评估合规性。

开发并部署此应用后，你会发现自己已经设置好以下示例 Web 应用以及所述的配置和安全措施。

## <a name="architecture-diagram-and-components"></a>体系结构示意图和组件
此应用是一个典型的 N 层应用程序，共有三层。 集成了监视和机密管理组件的前端、后端和数据库层如下所示：

![应用体系结构](./media/secure-pci-web-app/architecture.png)

该体系结构包括以下组件：

- [应用服务环境 v2](/app-service/environment/intro/)。 为我们的应用程序体系结构提供应用服务环境和负载均衡器。
- [Azure 应用程序网关](/application-gateway/)。 为应用程序体系结构提供网关和防火墙。
- [Application Insights](/azure-monitor/app/app-insights-overview)。 在多个平台上提供可扩展的应用程序性能管理 (APM) 服务。
- [Azure Key Vault](/key-vault/)。 存储并加密我们的应用的机密，并管理有关机密的访问策略的创建。
- [Azure Active Directory](/active-directory/fundamentals/active-directory-whatis/)。提供基于云的标识和访问管理服务、登录和访问资源。
- [Azure DNS](/dns/dns-overview)。 提供用来承载域的服务。
- [Azure 负载均衡器](/load-balancer/load-balancer-overview/)。 用来缩放应用程序，并实现服务的高可用性。
- [Azure 存储](/storage/common/storage-introduction/)。 提供针对新式数据存储方案的解决方案。
- [Azure Web 应用](/app-service/overview/)。  提供基于 HTTP 的服务来承载 Web 应用程序。
- [Azure Database for AzureSQL](/sql-database/sql-database-technical-overview/)。 安全地存储应用的数据。
<!-- Blueprints not available -->
- [Azure 安全中心](/security-center/)
- [Azure Policy](/governance/policy/overview)。 评估资源是否不符合分配的策略。

## <a name="threat-model"></a>威胁模型
威胁建模是一个过程，它需要先识别对业务和应用程序的潜在安全威胁，然后确保实施适当的缓解计划。

此示例使用了 [Microsoft Threat Modeling Tool](/security/azure-security-threat-modeling-tool) 来实施安全的示例应用的威胁建模。 通过绘制组件和数据流的关系图，你可以在开发过程的早期确定问题和威胁。 这在将来可以节省时间和开支。

下面是示例应用的威胁模型：

![威胁模型](./media/secure-pci-web-app/threat-model.png)

下面的屏幕截图显示了威胁建模工具生成的一些示例威胁和潜在漏洞。 威胁模型概述了暴露的受攻击面，并提示开发人员考虑如何缓解问题。

![威胁模型输出](./media/secure-web-app/threat-model-output.png)

例如，通过清理用户输入并使用 Azure Database for PostgreSQL 中的存储函数，可以减轻前面威胁模型输出中的 SQL 注入。 此缓解措施可防止在数据读取和写入期间随意执行查询。

开发人员通过缓解威胁模型输出中的每个威胁，提高系统的整体安全性。

## <a name="deployment"></a>部署
### <a name="prerequisites"></a>先决条件
若要启动并运行应用程序，需要安装以下工具：

- 用于修改和查看应用程序代码的代码编辑器。 [Visual Studio Code](https://code.visualstudio.com/) 是一个可供选择的开源编辑器。
- 在开发计算机上安装 [Azure CLI](/cli/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest)。
- 在系统上安装 [Git](https://git-scm.com/)。 Git 用于在本地克隆源代码。
- [jq](https://stedolan.github.io/jq/)，一个用于以用户友好方式查询 JSON 的 UNIX 工具。

此示例包含的 JSON 配置文件和 PowerShell 脚本可供 Azure 资源管理器的 API 服务用来在 Azure 中部署资源。 [此处](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM)提供了详细的部署说明。

#### <a name="quickstart"></a>快速入门

1.  将[此](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) GitHub 存储库克隆或下载到本地工作站。
2.  查看 0-Setup-AdministrativeAccountAndPermission.md 并运行所提供的命令。
3.  使用 Contoso 示例数据部署测试解决方案，或试用初始生产环境。

    此脚本部署了 Azure 资源，借以通过 Contoso 示例数据展示网上商店。

在此示例中，你运行部署脚本，该脚本将 Web 应用部署到应用服务并创建资源。

有许多方法可在 Azure 上部署应用，其中包括：

- Azure Resource Manager 模板
- PowerShell
- Azure CLI
- Azure 门户

## <a name="guidance-and-recommendations"></a>指导和建议

### <a name="network"></a>网络
体系结构定义了一个地址空间为 10.200.0.0/16 的专用虚拟网络。
 ![Virtual_Network](./media/secure-pci-web-app/virtual-network.png)

### <a name="network-security-groups"></a>网络安全组
[网络安全组](/virtual-network/virtual-networks-nsg)包含允许或拒绝虚拟网络中流量的访问控制列表 (ACL)。 网络安全组可用于在子网或单个 VM 级别保护流量。 存在以下网络安全组：

- 应用程序网关有 1 个网络安全组
- 应用服务环境有 1 个网络安全组
- Azure SQL 数据库有 1 个网络安全组
- 守护主机有 1 个网络安全组

每个网络安全组打开了特定的端口和协议，使解决方案能够安全正确地工作。 此外，为每个网络安全组启用了以下配置：

- 诊断日志和事件<!-- not available--> 已启用并存储在存储帐户中
- [Azure Monitor 日志已连接到网络安全组的诊断](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>NSG 配置
应当按下图所示配置应用服务环境的 NSG 配置。

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

每个子网与其对应的网络安全组相关联。

### <a name="config"></a>Config
按下图所示配置子网。
 ![Config](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>Azure DNS
域名系统 (DNS) 负责将网站或服务名称转换（或解析）为它的 IP 地址。 [Azure DNS](/dns/dns-overview) 是 DNS 域的托管服务，它使用 Azure 基础结构提供名称解析。 通过在 Azure 中托管域，用户可以使用与其他 Azure 服务相同的凭据、API、工具和账单来管理 DNS 记录。 Azure DNS 还支持 DNS 专用域。

### <a name="protect-data"></a>保护数据
为了帮助保护云中的数据，需要考虑数据可能出现的状态以及可用于该状态的控件。 Azure 数据安全与加密的最佳做法与以下数据状态相关：

- 静态：包括物理媒体（磁盘或光盘）上以静态方式存在的所有信息存储对象、容器和类型。
- 传输中：在各组件、位置或程序间传输数据时，数据处于“传输中”状态。 例如通过网络、通过服务总线（从本地到云，反之亦然，包括诸如 ExpressRoute 的混合连接）进行传输，或在输入/输出过程。

### <a name="azure-storage"></a>Azure 存储
为了满足静态数据加密要求，所有 [Azure 存储](https://www.azure.cn/pricing/calculator/storage/)都使用 Azure Key Vault 来控制用来访问和加密数据的密钥。 这有助于保护持卡人数据，以支持 PCI DSS 3.2 定义的组织安全承诺和符合性要求。

### <a name="azure-disk-encryption"></a>Azure 磁盘加密
Azure 磁盘加密利用 Windows 的 BitLocker 功能，为数据磁盘提供卷加密。 此解决方案与 Azure Key Vault 集成，可帮助控制和管理磁盘加密密钥。

### <a name="azure-sql-database"></a>Azure SQL 数据库
Azure SQL 数据库实例使用以下数据库安全措施：

- 使用 [Active Directory 身份验证和授权](/sql-database/sql-database-aad-authentication)可在一个中心位置集中管理数据库用户和其他 Microsoft 服务的标识。
- [SQL 数据库审核](/sql-database/sql-database-auditing-get-started/)跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。
- Azure SQL 数据库配置为使用[透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)，它对数据库、相关备份和事务日志文件进行实时加密和解密，以保护静态信息。 透明数据加密可确保存储的数据免遭他人未经授权的访问。
- 在授予相应的权限前，[防火墙规则](/sql-database/sql-database-firewall-configure)会阻止对数据库服务器的所有访问。 防火墙基于每个请求的起始 IP 地址授予数据库访问权限。
- [SQL 威胁检测](/sql-database/sql-database-threat-detection-get-started)可以通过为可疑数据库活动、潜在漏洞、SQL 注入攻击和异常数据库访问模式提供安全警报，在发生潜在威胁时进行检测和响应。
- [加密列](/sql-database/sql-database-always-encrypted-azure-key-vault)可以确保敏感数据永远不会在数据库系统中以明文形式显示。 启用数据加密后，只有具有密钥访问权限的客户端应用程序或应用程序服务器才能访问明文数据。
- [SQL 数据库动态数据掩码](/sql-database/sql-database-dynamic-data-masking-get-started)通过对非特权用户或应用程序模糊化敏感数据来限制此类数据的泄漏。 动态数据掩码可以自动发现潜在的敏感数据，并建议应用合适的掩码。 这有助于识别和减少数据访问，避免通过未经授权的访问退出数据库。 客户需要负责调整动态数据掩码设置，使之遵循其数据库架构。

### <a name="identity-management"></a>身份管理
以下技术在 Azure 环境中提供用于管理持卡人数据访问的功能：

- Azure Active Directory 是 Microsoft 提供的多租户基于云的目录和标识管理服务。 解决方案的所有用户（包括访问 Azure SQL 数据库的用户）都在 Azure Active Directory 中创建。
- <!-- not available -->数据库列加密使用 Azure Active Directory 对访问 Azure SQL 数据库的应用程序进行身份验证。 有关详细信息，请参阅[如何保护 Azure SQL 数据库中的敏感数据](/sql-database/sql-database-security-overview)。
- Azure 基于角色的访问控制使管理员能够定义细致的访问权限，仅授予用户执行作业所需的访问权限。 无需向每个用户授予 Azure 资源的不受限权限，管理员可以只允许使用特定的操作来访问持卡人数据。 订阅访问仅限于订阅管理员。
- Azure Active Directory Privileged Identity Management 使客户能够最大限度地减少有权访问持卡人数据等特定信息的用户数量。 管理员可以使用 Azure Active Directory Privileged Identity Management 来发现、限制和监视特权标识及其对资源的访问。 还可以根据需要，使用此功能来实施按需、实时的管理访问。
- Azure Active Directory 标识保护会检测到影响组织标识的潜在漏洞，配置自动化的措施来应对所检测到的与组织标识相关的可疑操作，调查可疑的事件以采取相应的措施予以解决。

### <a name="secrets-management"></a>机密管理
此解决方案使用 Azure Key Vault 来管理密钥和机密。 Azure 密钥保管库可帮助保护云应用程序和服务使用的加密密钥和机密。 以下 Azure Key Vault 功能可帮助客户保护和访问此类数据：

- 根据需要配置高级访问权限策略。
- 使用对密钥和机密所需的最低权限来定义 Key Vault 访问策略。
- Key Vault 中的所有密钥和机密都有过期日期。
- Key Vault 中的所有密钥受专用硬件安全模块的保护。 密钥类型为受 HSM 保护的 2048 位 RSA 密钥。
- 通过 Key Vault，可以使用受硬件安全模块 (HSM) 保护的密钥，来加密密钥和机密（例如身份验证密钥、存储帐户密钥、数据加密密钥、.PFX 文件和密码）。
- 使用 RBAC 向用户、组和应用程序分配对特定作用域的权限。
- 使用 Key Vault 和自动续订来管理 TLS 证书。
- Key Vault 的诊断日志已启用，其保留期至少为 365 天。
- 对密钥进行允许的加密操作时，仅限必需的操作。

### <a name="azure-security-center"></a>Azure 安全中心
借助 Azure 安全中心，客户可在工作负荷中集中应用和管理安全策略、减少项目暴露给威胁的几率，以及检测和应对攻击。 此外，Azure 安全中心会访问 Azure 服务的现有配置，以提供配置与服务建议来帮助改善安全状况和保护数据。

Azure 安全中心使用各种检测功能，提醒客户针对其环境的潜在攻击。 这些警报包含有关触发警报的内容、目标资源以及攻击源的重要信息。 Azure 安全中心有一组预定义的安全警报，这些警报在出现威胁或可疑活动时触发。 客户可以使用 Azure 安全中心的自定义警报规则，根据从环境中收集到的数据定义新的安全警报。

Azure 安全中心提供区分优先级的安全警报和事件，让客户更轻松地发现和解决潜在安全问题。 针对检测到的每种威胁生成威胁情报报告，帮助事件响应团队调查和解决威胁。

### <a name="azure-application-gateway"></a>Azure 应用程序网关
体系结构使用配置了 Web 应用程序防火墙并启用了 OWASP 规则集的应用程序网关，来降低安全漏洞风险。 其他功能包括：

- 端到端 SSL
- 禁用 TLS v1.0 和 v1.1
- 启用 TLSv1.2
- Web 应用程序防火墙（预防模式）
- 采用 OWASP 3.0 规则集的预防模式
- 启用诊断日志记录
- 自定义运行状况探测
- Azure 安全中心和 Azure 顾问提供了额外的保护和通知。 Azure 安全中心还提供信誉系统。

### <a name="logging-and-auditing"></a>日志记录和审核
Azure 服务广泛记录系统和用户活动以及系统运行状况：

- [活动日志](/monitoring-and-diagnostics/monitoring-overview-activity-logs)提供对订阅中资源执行的操作的深入信息。 活动日志可帮助确定操作的发起方、发生的时间和状态。
- [诊断日志](/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)包括每个资源发出的所有日志。 这些日志包括 Windows 事件系统日志、Azure 存储日志、Key Vault 审核日志以及应用程序网关访问和防火墙日志。 所有诊断日志都将写入到集中式加密 Azure 存储帐户以进行存档。 保留期是允许用户配置的，最长为 730 天，具体取决于组织的保留期要求。

### <a name="azure-monitor-logs"></a>Azure Monitor 日志
这些日志将整合到 [Azure Monitor 日志](https://www.azure.cn/pricing/details/monitor/)中进行处理、存储以及在仪表板上报告。 收集后，数据在 Log Analytics 工作区内按数据类型整理到不同的表中，这样即可不考虑最初来源而集中分析所有数据。 此外，Azure 安全中心与 Azure Monitor 日志集成，使客户能够使用 Kusto 查询来访问其安全事件数据，并将这些数据与其他服务中的数据合并。

此体系结构中包括了以下 Azure [监视解决方案](/log-analytics/log-analytics-add-solutions)：

- [Active Directory 评估](/log-analytics/log-analytics-ad-assessment)：Active Directory 运行状况检查解决方案按固定时间间隔评估服务器环境的风险和运行状况，并且提供特定于部署的服务器基础结构的优先建议列表。
- [SQL 评估](/log-analytics/log-analytics-sql-assessment)：SQL 运行状况检查解决方案按固定时间间隔评估服务器环境的风险和运行状况，并为客户提供特定于部署的服务器基础结构的优先建议列表。
- [代理运行状况](/operations-management-suite/oms-solution-agenthealth)：代理运行状况解决方案报告已部署代理的数量及其地理分布，以及无响应的代理数量和提交操作数据的代理数量。
- [Activity Log Analytics](/log-analytics/log-analytics-activity)：Activity Log Analytics 解决方案可帮助分析客户的所有 Azure 订阅的 Azure 活动日志。

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](/monitoring-and-diagnostics/) 通过使组织能够审核、创建警报和存档数据（包括在用户的 Azure 资源中跟踪 API 调用），帮助用户跟踪性能、维护安全性和确定趋势。

### <a name="application-insights"></a>Application Insights
[Application Insights](/application-insights/app-insights-overview) 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理服务。 Application Insights 可检测性能异常，客户可以使用它来监视实时 Web 应用程序。 它包含强大的分析工具来帮助客户诊断问题，了解用户在应用中实际执行了哪些操作。 它旨在帮助客户持续提高性能和可用性。

### <a name="azure-key-vault"></a>Azure Key Vault
为组织创建一个保管库，以便在其中存储密钥，使如下所示的操作任务始终能够追责：

- Key Vault 中存储的数据包括：

   - Application Insights 密钥
   - 数据存储访问密钥
   - 连接字符串
   - 数据表名称
   - 用户凭据

- 根据需要配置高级访问权限策略
- 在定义 Key Vault 访问策略时，尽量将访问密钥和机密的必需权限降至最低
- Key Vault 中的所有密钥和机密都有过期日期
- Key Vault 中的所有密钥都受 HSM 保护 [密钥类型 = 受 HSM 保护的 2048 位 RSA 密钥]
- 使用基于角色的访问控制 (RBAC) 向所有用户/标识授予了最低必需权限
- 应用程序共享 Key Vault 的前提是彼此信任且需要在运行时访问相同的机密
- Key Vault 的诊断日志已启用，其保留期至少为 365 天。
- 对密钥进行允许的加密操作时，仅限必需的操作

### <a name="vpn-and-expressroute"></a>VPN 和 ExpressRoute
需要配置安全 VPN 隧道或 [ExpressRoute](/expressroute/expressroute-introduction)，以安全地建立与作为此 PaaS Web 应用程序参考体系结构的一部分部署的资源的连接。 通过适当设置 VPN 或 ExpressRoute，客户可以为传输中的数据添加一层保护。

在 Azure 中实施安全 VPN 隧道，可在本地网络与 Azure 虚拟网络之间创建虚拟专用连接。 此连接通过 Internet 进行，可让客户在其网络与 Azure 之间的加密链路内通过“隧道”安全地传输信息。 站点到站点 VPN 是安全且成熟的技术，各种规模的企业已部署该技术数十年。 此选项使用 IPsec 隧道模式作为加密机制。

由于 VPN 隧道中的流量会通过站点到站点 VPN 在 Internet 上遍历，Microsoft 提供了另一个更安全的连接选项。 Azure ExpressRoute 是 Azure 与本地位置或 Exchange 托管提供商之间专用的 WAN 链接。 ExpressRoute 连接并不绕过 Internet，并且与通过 Internet 的典型连接相比，这些连接可靠性更高、速度更快、延迟时间更短且安全性更高。 此外，由于使用的是客户电信提供商的直接连接，数据不会通过 Internet 遍历，因此不会在 Internet 上公开。

<!-- not available-->

## <a name="cost-considerations"></a>成本注意事项
如果还没有 Azure 帐户，可以创建一个免费帐户。 转到[试用版帐户页面](https://www.microsoft.com/china/azure/index.html?fromtype=cn)以开始创建，了解可以使用免费 Azure 帐户执行哪些操作，并了解哪些产品可以免费使用 12 个月。

若要为示例应用中的资源部署安全功能，你需要付费购买一些高级功能。 随着应用不断扩展，你需要升级 Azure 提供的免费层和试用版来满足应用程序需求，你的成本可能会增加。 使用 Azure [定价计算器](https://www.azure.cn/pricing/calculator/)来估算成本。

## <a name="next-steps"></a>后续步骤
以下文章可帮助你设计、开发和部署安全的应用程序。

- [设计](secure-design.md)
- [开发](secure-develop.md)
- [部署](secure-deploy.md)
