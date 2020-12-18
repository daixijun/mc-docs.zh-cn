---
title: 通过使用 Azure Active Directory 中的连续访问评估来构建复原能力
description: 适用于架构师和 IT 管理员的 CAE 使用指南
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
ms.openlocfilehash: ebde6caad22023f32f71ec6032472cf49fa8916e
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97004315"
---
# <a name="build-resilience-by-using-continuous-access-evaluation"></a>通过使用连续访问评估来构建复原能力

利用[连续访问评估](/active-directory/conditional-access/concept-continuous-access-evaluation) (CAE)，Azure AD 应用程序可以订阅严重事件，然后，这些事件可以被评估，并且相应的措施可以被强制执行。 其中包括对以下事件进行评估：

* 删除或禁用用户帐户

* 更改用户的密码

* 为用户启用 MFA。

* 管理员显式撤销令牌。

* 检测到用户风险提升。

因此，应用程序可以根据 Azure AD 为其发出信号的事件来拒绝尚未过期的令牌，如下图中所示。

![CAE 的概念图](./media/resilience-with-cae/admin-resilience-continuous-access-evaluation.png)

## <a name="how-does-cae-help"></a>CAE 如何提供帮助？

此机制允许 Azure AD 颁发生存期较长的令牌，同时使应用程序能够只在需要时才撤销访问权限并强制重新进行身份验证。 此模式的最终结果是减少了用于获取令牌的调用，这就意味着端到端流会更具复原能力。 

若要使用 CAE，服务和客户端必须都支持 CAE。 Microsoft 365 服务（如 Exchange Online、Teams 和 SharePoint Online）支持 CAE。 在客户端，使用这些 Office 365 服务的基于浏览器的体验（例如 Outlook Web 应用）和 Office 365 本机客户端的特定版本支持 CAE。 更多的 Azure 云服务将会支持 CAE。

Microsoft 正在与业界合作构建[标准](https://openid.net/wg/sse/)，这些标准将允许第三方应用程序使用此功能。 你也可以开发支持 CAE 的应用程序。 有关详细信息，请参阅“如何在应用程序中构建复原能力”。

## <a name="how-do-i-implement-cae"></a>如何实现 CAE？

* 在 Azure AD 安全配置中[启用 CAE](/active-directory/conditional-access/concept-continuous-access-evaluation)。

* 请确保你的组织使用的是 Microsoft Office 本机应用程序的[兼容版本](/active-directory/conditional-access/concept-continuous-access-evaluation)。

## <a name="next-steps"></a>后续步骤
面向管理员和架构师的复原能力资源
 
* [通过凭据管理来构建复原能力](resilience-in-credentials.md)

* [在混合身份验证中构建复原能力](resilience-in-hybrid.md)

面向开发人员的复原能力资源

* [在应用程序中构建标识和访问管理复原能力](resilience-app-development-overview.md)

* [在 CIAM 系统中构建复原能力](resilience-b2c.md)

