---
title: MFA 或 2FA 和 Privileged Identity Management - Azure AD | Microsoft Docs
description: 了解 Azure AD Privileged Identity Management (PIM) 如何验证多重身份验证 (MFA)。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 12/08/2020
ms.author: v-junlch
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd41a7ad54c7a08577ed6f0d0ecd006c95a261f4
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97004046"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>多重身份验证和 Privileged Identity Management

我们建议要求所有管理员使用多重身份验证 (MFA)。 这可降低因密码泄露而受到攻击的风险。

可以要求用户在登录时完成多重身份验证质询。 还可以要求用户在 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 中激活角色时完成多重身份验证质询。 这样一来，如果用户在登录时未完成多重身份验证质询，Privileged Identity Management 会提示他们完成此操作。

> [!IMPORTANT]
> 目前，Azure AD 多重身份验证仅适用于工作或学校帐户，不适用于 Microsoft 个人帐户（通常是用于登录 Skype、Xbox 或 Outlook.com 等 Microsoft 服务的个人帐户）。 因此，使用个人帐户的任何人都不是符合条件的管理员，因为他们无法使用多重身份验证激活其角色。 如果这些用户需要继续使用 Microsoft 帐户管理工作负荷，请立即将其提升到永久管理员。

## <a name="how-pim-validates-mfa"></a>PIM 如何验证 MFA

当用户激活角色时，有两个选项可用于验证多重身份验证。

最简单的选项是对激活特权角色的用户使用 Azure AD 多重身份验证。 为此，请首先检查这些用户是否已获得许可（如有必要），以及是否已注册 Azure AD 多重身份验证。 若要详细了解如何部署 Azure AD 多重身份验证，请参阅[部署基于云的 Azure AD 多重身份验证](../authentication/howto-mfa-getstarted.md)。 建议（但不是必需）将 Azure AD 配置为在用户登录时针对这些用户强制执行多重身份验证。 这是因为多重身份验证检查将由 Privileged Identity Management 本身进行。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中配置 Azure AD 角色设置](pim-how-to-change-default-settings.md)
- [在 Privileged Identity Management 中配置 Azure 资源角色设置](pim-resource-roles-configure-role-settings.md)

