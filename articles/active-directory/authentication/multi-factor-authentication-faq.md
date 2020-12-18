---
title: Azure AD 多重身份验证常见问题解答 - Azure Active Directory
description: 与 Azure AD 多重身份验证相关的常见问题与解答。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 12/09/2020
ms.author: v-junlch
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5426d90ac2f3114f11aa476386c5ef02e5f1815
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97003798"
---
# <a name="frequently-asked-questions-about-azure-ad-multi-factor-authentication"></a>有关 Azure AD 多重身份验证的常见问题

本“常见问题解答”文章解答有关 Azure AD 多重身份验证和使用多重身份验证服务的常见问题。 其中的问题已划分为常规服务问题、计费模式问题、用户体验问题和故障排除问题。

## <a name="general"></a>常规

## <a name="billing"></a>计费
可参阅 [多重身份验证定价页](https://www.azure.cn/pricing/details/multi-factor-authentication/)获得大多数计费问题的答案。

### <a name="is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication"></a>通过电话或短信进行身份验证时，我的组织是否需要付费？

否。对于通过 Azure AD 多重身份验证拨打的每个电话或向用户发送的每条短信，都不需要付费。 

用户可能需要为收到的电话或短信支付费用，取决于个人电话服务。


## <a name="manage-and-support-user-accounts"></a>管理和支持用户帐户

### <a name="what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone"></a>如果用户的手机未收到响应，我该告诉他们怎么做？

让用户在 5 分钟内最多尝试 5 次，以便收到用于身份验证的电话或短信。 Microsoft 使用多个提供程序，用于进行呼叫和发送短信。 如果此方法不起作用，请创建支持案例进行进一步的故障排除。

第三方安全应用程序也可能会阻止验证代码短信或电话呼叫。 如果使用第三方安全应用，请尝试禁用保护，然后请求发送另一个 MFA 验证代码。

如果上述步骤不起作用，请检查是否为用户配置了多个验证方法。 再次尝试登录，但需要在登录页上选择另一种验证方法。

有关详细信息，请参阅[最终用户故障排除指南](../user-help/multi-factor-authentication-end-user-troubleshoot.md)。

### <a name="what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account"></a>如果某个用户无法进入其帐户，我该怎么做？

可以要求用户再次完成注册过程来重置其帐户。 详细了解如何[管理云中 Azure AD 多重身份验证的用户和设备设置](howto-mfa-userdevicesettings.md)。

### <a name="my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out"></a>我的用户说，有时他们收不到短信，或者验证超时。

短信送达无法得到保障，因为存在可能影响服务可靠性的不可控因素。 这些因素包括目标国家/地区、移动电话运营商和信号强度。

第三方安全应用程序也可能会阻止验证代码短信或电话呼叫。 如果使用第三方安全应用，请尝试禁用保护，然后请求发送另一个 MFA 验证代码。

如果用户经常难以可靠地接收短信，请告诉他们改用 Microsoft Authenticator 应用或电话验证方法。 Microsoft Authenticator 应用可以通过手机网络和 Wi-Fi 连接接收通知。 此外，即使设备根本没有信号，也可以生成验证码。 Microsoft Authenticator 应用适用于 [Android](https://go.microsoft.com/fwlink/?Linkid=825072)、[iOS](https://go.microsoft.com/fwlink/?Linkid=825073) 和 [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)。

### <a name="why-are-my-users-being-prompted-to-register-their-security-information"></a>为何系统提示用户注册其安全信息？

有多种原因会导致系统提示用户注册其安全信息：

- 该用户的管理员已在 Azure AD 中为其启用 MFA，但没有为其帐户注册安全信息。
- 该用户已在 Azure AD 中启用自助密码重置。 以后如果用户忘记了密码，安全信息可帮助他们重置密码。
- 该用户访问的应用程序有一个要求使用 MFA 的条件访问策略，但此应用程序以前未注册 MFA。
- 该用户正在将某个设备注册到 Azure AD（包括 Azure AD 加入），并且你的组织要求使用 MFA 进行设备注册，但该用户以前未注册 MFA。
- 该用户正在 Windows 10 中生成 Windows Hello 企业版（需要 MFA），但以前未注册 MFA。
- 组织已创建并启用一个 MFA 注册策略，该策略已应用到该用户。
- 该用户以前已注册 MFA，但选择的验证方法后来被管理员禁用。 因此，该用户必须再次完成 MFA 注册，以选择新的默认验证方法。

## <a name="errors"></a>错误

* [如果用户在使用移动应用通知时看到“身份验证请求不适用于已激活的帐户”错误消息，他们该怎么办？](#what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications)
* [如果用户在登录非浏览器应用程序时看到 0x800434D4L 错误消息，他们该怎么办？](#what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application)

### <a name="what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications"></a>如果用户在使用移动应用通知时看到“身份验证请求不适用于已激活的帐户”错误消息，他们该怎么办？

要求用户完成以下过程以从 Microsoft Authenticator 中删除其帐户，并再次添加其帐户：

1. 转到[其 Azure 门户配置文件](https://account.activedirectory.windowsazure.cn/profile/)，并使用组织帐户登录。
2. 选择“其他安全性验证” 。
3. 从 Microsoft Authenticator 应用中删除现有帐户。
4. 单击“配置”，并按照说明重新配置 Microsoft Authenticator。

### <a name="what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application"></a>如果用户在登录非浏览器应用程序时看到 0x800434D4L 错误消息，该怎么办？

如果尝试登录在本地计算机上安装的非浏览器应用程序，并且此应用程序无法使用需要双重验证的帐户，则将发生 0x800434D4L 错误。

此错误的解决方法是，使用不同的用户帐户执行管理员相关操作和非管理员操作。 稍后，可以在管理员帐户与非管理员帐户之间链接邮箱，以便能够使用非管理员帐户登录到 Outlook。 若要详细了解此解决方案，请了解如何[让管理员能够打开和查看用户邮箱的内容](https://help.outlook.com/141/gg709759.aspx?sl=1)。

## <a name="next-steps"></a>后续步骤

如果此处未解答你的问题，则可以使用以下支持选项：

* 在 [Microsoft 支持知识库](https://support.microsoft.com)中搜索常见技术问题的解决方法。
* 在社区中搜索和浏览技术问题与答案，或者在 [Azure Active Directory 问答](https://docs.microsoft.com/answers/topics/azure-active-directory.html)中提出自己的问题。
* 通过 [Azure 多重身份验证服务器支持](https://support.microsoft.com/oas/default.aspx?prid=14947)联系 Microsoft 专业人员。 与我们联系时，尽可能包含有关问题的更多信息将很有帮助。 可提供的信息包括看到错误的页面、特定错误代码、特定会话 ID 和看到错误的用户的 ID。

