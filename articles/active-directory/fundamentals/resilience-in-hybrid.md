---
title: 在 Azure Active Directory 中构建更易复原的混合身份验证
description: 指导架构师和 IT 管理员构建可复原的混合基础结构。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: v-junlch
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec5dd33d1501151a4a915bd3db4bb58956681b19
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97004313"
---
# <a name="build-resilience-in-your-hybrid-architecture"></a>在混合体系结构中构建复原能力

混合身份验证允许用户使用其在本地管理的标识来访问基于云的资源。 混合基础结构包括云组件和本地组件。

* 云组件包括 Azure AD、Azure 资源和服务、组织的基于云的应用和 SaaS 应用程序。

* 本地组件包括本地应用程序、资源（例如 SQL 数据库）和标识提供者（例如 Windows Server Active Directory）。 

> [!IMPORTANT]
> 规划混合基础结构的复原能力时，关键是要尽量减少依赖项和单一故障点。 

Microsoft 提供三种混合身份验证机制。 这些选项按复原能力顺序列出。 建议尽可能实现密码哈希同步。

* [密码哈希同步](../hybrid/whatis-phs.md) (PHS) 使用 Azure AD Connect 将标识和密码哈希同步到 Azure AD，使用户能够使用本地管理的密码登录到基于云的资源。 PHS 具有本地依赖项，仅用于同步，不用于身份验证。

* [联合身份验证](../hybrid/whatis-fed.md)客户部署联合身份验证服务（例如 AD FS），然后 Azure AD 验证联合身份验证服务生成的 SAML 断言。 联合身份验证对本地基础结构的依赖性最高，因此故障点更多。 

   
你可能会在组织中使用这些方法中的一种或多种。 本文包含一个决策树，可帮助你决定如何选择这些方法。

## <a name="password-hash-synchronization"></a>密码哈希同步

Azure AD 最简单、最易复原的混合身份验证选项是[密码哈希同步](../hybrid/whatis-phs.md)，它在处理身份验证请求时没有任何本地标识基础结构依赖项。 在将具有密码哈希的标识同步到 Azure AD 后，用户即可不依赖本地标识组件，向云资源进行身份验证。 

![PHS 体系结构关系图](./media/resilience-in-hybrid/admin-resilience-password-hash-sync.png)

如果选择此身份验证选项，当本地标识组件不可用时，不会出现中断。 发生本地中断的原因有很多，包括硬件故障、电源中断、自然灾害和恶意软件攻击。 

### <a name="how-do-i-implement-phs"></a>如何实现 PHS？

若要实现 PHS，请参阅以下资源：

* [使用 Azure AD Connect 实现密码哈希同步](../hybrid/how-to-connect-password-hash-synchronization.md)

* [启用密码哈希同步](../hybrid/how-to-connect-password-hash-synchronization.md)

## <a name="federation"></a>联合

联合身份验证涉及在 Azure AD 与联合身份验证服务之间创建信任关系，其中包括终结点、令牌签名证书和其他元数据的交换。 请求进入 Azure AD 时，它会读取配置，并将用户重定向到配置的终结点。 此时，用户将与联合身份验证服务进行交互，后者会发出由 Azure AD 验证的 SAML 断言。 

下图显示了企业 Active Directory 联合身份验证服务 (AD FS) 的拓扑，该部署包含跨多个本地数据中心的冗余联合身份验证和 Web 应用程序代理服务器。 此配置依赖于企业网络基础结构组件，例如 DNS、具有地理关联功能的网络负载均衡、防火墙等。所有本地组件和连接都容易出现故障。 有关详细信息，请访问 [AD FS 产能规划文档](https://docs.microsoft.com/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity)。

> [!NOTE]
>  联合身份验证的本地依赖项最多，因此潜在的故障点也最多。 虽然此图显示的是 AD FS，但其他本地标识提供者可能会根据类似的设计注意事项来实现高可用性、可伸缩性和故障转移。

![联合身份验证的体系结构关系图](./media/resilience-in-hybrid/admin-resilience-federation.png)

 ### <a name="how-do-i-implement-federation"></a>如何实现联合身份验证？

如果要实现联合身份验证策略，或者想使其更易复原，请参阅以下资源。

* [什么是联合身份验证](../hybrid/whatis-fed.md)

* [联合身份验证的工作原理](../hybrid/how-to-connect-fed-whatis.md)

* [Azure AD 联合身份验证兼容性列表](../hybrid/how-to-connect-fed-compatibility.md)

* 遵循 [AD FS 产能规划文档](https://docs.microsoft.com/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity)

* [在 Azure IaaS 中部署 AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)

## <a name="next-steps"></a>后续步骤
面向管理员和架构师的复原能力资源
 
* [利用凭据管理构建复原能力](resilience-in-credentials.md)

* [通过使用连续访问评估 (CAE) 来构建复原能力](resilience-with-continuous-access-evaluation.md)

面向开发人员的复原能力资源

* [在应用程序中构建 IAM 复原能力](resilience-app-development-overview.md)

* [在 CIAM 系统中构建复原能力](resilience-b2c.md)

