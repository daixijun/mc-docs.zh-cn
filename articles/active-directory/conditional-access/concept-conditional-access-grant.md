---
title: 条件访问策略中的授权控制 - Azure Active Directory
description: Azure AD 条件访问策略中的授权控制是什么
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: v-junlch
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e5baac79267b4072ef60e06829751f384fbf0ce
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97003792"
---
# <a name="conditional-access-grant"></a>条件访问：授予

在条件访问策略中，管理员可以利用访问控制来授予或阻止对资源的访问权限。

![包含授权控制且需要多重身份验证的条件访问策略](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>阻止访问

阻止操作会考虑到任何分配，根据条件访问策略配置阻止访问。

阻止是一种强有力的控制，运用此项控制时，应具备相应的知识。 带有块语句的策略可能会产生意外的副作用。 在大规模启用之前，正确的测试和验证至关重要。 进行更改时，管理员应使用[条件访问中的 What If 工具](what-if-tool.md)之类的工具。

## <a name="grant-access"></a>授予访问权限

在授予访问权限时，管理员可以选择强制实施一项或多项控制。 这些控制包括以下选项： 

- [需要多重身份验证（Azure AD 多重身份验证）](../authentication/concept-mfa-howitworks.md)
- [“需要已批准的客户端应用”](app-based-conditional-access.md)
- [要求更改密码](#require-password-change)

当管理员组合使用这些选项时，可以选择以下方法：

- 需要所有选定控制（控制 **和** 控制）
- 需要某一选定控制（控制 **或** 控制）

默认情况下，条件访问需要所有选定控制。

### <a name="require-multi-factor-authentication"></a>需要多重身份验证

选中此复选框会要求用户执行 Azure AD 多重身份验证。 在[规划基于云的 Azure AD 多重身份验证部署](../authentication/howto-mfa-getstarted.md)一文中可以找到有关部署 Azure AD 多重身份验证的详细信息。


### <a name="require-approved-client-app"></a>需要批准的客户端应用

组织可以要求只能尝试从已批准的客户端应用访问选定的云应用。 这些已批准的客户端应用支持 [Intune 应用保护策略](https://docs.microsoft.com/intune/app-protection-policy)，而不受任何移动设备管理 (MDM) 解决方案影响。

为了利用此授权控制，条件访问要求在 Azure Active Directory 中注册设备，这需要使用代理应用。 代理应用可以是适用于 iOS 的 Microsoft Authenticator，也可以是 Microsoft Authenticator 或适用于 Android 设备的 Microsoft 公司门户。 如果用户尝试进行身份验证时设备上未安装代理应用，则会将用户重定向到相应的应用商店来安装所需的代理应用。

此设置适用于以下 iOS 和 Android 应用：

- Azure 信息保护
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Power Automate
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype for Business
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- 微软待办
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Microsoft Whiteboard

**备注**

- 批准的客户端应用支持 Intune 移动应用管理功能。
- “需要批准的客户端应用”要求：
   - 仅支持 iOS 和 Android 作为设备平台条件。
   - 注册设备需要代理应用。 代理应用可以是适用于 iOS 的 Microsoft Authenticator，也可以是 Microsoft Authenticator 或适用于 Android 设备的 Microsoft 公司门户。
- 条件访问无法将 InPrivate 模式下的 Microsoft Edge 视为已批准的客户端应用。

请参阅文章[如何：使用条件访问要求使用批准的设备应用访问云应用](app-based-conditional-access.md)，以获取配置示例。

### <a name="require-password-change"></a>要求更改密码 

如果检测到用户风险，则使用用户风险策略条件，管理员可以选择让用户通过 Azure AD 自助式密码重置来安全地更改密码。 如果检测到用户风险，用户可以执行自助式密码重置进行自我修正，这将关闭用户风险事件，以避免为管理员带来不必要的干扰。 

当系统提示用户更改其密码时，他们首先需要完成多重身份验证。 你需要确保所有用户都已注册多重身份验证，为其帐户检测到风险做准备。  

> [!WARNING]
> 用户必须预先注册了自助式密码重置，然后才能触发用户风险策略。 

使用密码更改控件来配置策略时，存在几个限制。  

1. 必须将策略分配到“所有云应用”。 这可以防止攻击者使用不同的应用更改用户的密码并重置帐户的风险，只需登录到不同的应用即可。 
1. 要求密码更改不能与其他控件一起使用，例如，要求设备符合规范。  
1. 密码更改控件只能与用户和组分配条件、云应用分配条件（必须设置为“全部”）和用户风险条件一起使用。 

### <a name="terms-of-use"></a>使用条款

如果你的组织已创建使用条款，则授权控制下可能会显示其他选项。 管理员可以通过这些选项要求用户确认使用条款，作为访问受策略保护的资源的条件。

## <a name="next-steps"></a>后续步骤

- [条件访问：会话控制](concept-conditional-access-session.md)

- [条件访问常见策略](concept-conditional-access-policy-common.md)

