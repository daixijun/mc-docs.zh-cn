---
title: 什么是 Azure Active Directory 中的 B2B 协作？
description: Azure Active Directory B2B 协作支持来宾用户访问权限，以便安全地与外部合作伙伴共享资源和协作。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 11/09/2020
ms.author: v-junlch
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: c20dde620eec2004ca7afe1c1c0990bae49b1a6a
ms.sourcegitcommit: 59810f8eba5e430d85a595e346d3b7fb6e4a0102
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2020
ms.locfileid: "94501662"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>什么是 Azure Active Directory B2B 中的来宾用户访问权限？

Azure Active Directory (Azure AD) 企业到企业 (B2B) 协作是外部标识的一项功能，使你能够邀请来宾用户同组织一起协作。 使用 B2B 协作，可以安全地将公司的应用程序和服务与来自任何其他组织的来宾用户共享，同时保持对自己公司数据的控制。 与外部合作伙伴安全放心地合作，不论其规模是大是小，甚至就算他们没有 Azure AD 或 IT 部门也无妨。 合作伙伴通过一个简单的邀请和兑换过程即可使用自己的凭据来访问公司资源。 开发人员可以使用 Azure AD 企业到企业 API 自定义邀请过程或编写应用程序。 有关与来宾用户相关的许可和定价信息，请参阅 [Azure Active Directory 定价](https://www.azure.cn/pricing/details/active-directory/)。  


## <a name="collaborate-with-any-partner-using-their-identities"></a>与使用自己标识的任何合作伙伴协作

借助 Azure AD B2B，合作伙伴可使用自己的标识管理解决方案，因此组织省去了外部管理开销。 来宾用户可使用自己的工作或学校标识登录应用和服务。

- 合作伙伴使用自己的标识和凭据；
- 不需要管理外部帐户或密码。
- 不需要同步帐户或管理帐户生命周期。  

## <a name="easily-invite-guest-users-from-the-azure-ad-portal"></a>从 Azure AD 门户轻松地邀请来宾用户

管理员可以在 Azure 门户中轻松地向组织添加来宾用户。

- 在 Azure AD 中创建新的来宾用户，方法类似于添加新用户。
- 将来宾用户分配到应用或组。
- 发送包含兑换链接的邀请电子邮件或发送要共享的应用的直接链接。

![显示“新建来宾用户邀请”入口页的屏幕截图](./media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

- 来宾用户按照几个简单的[兑换步骤](redemption-experience.md)操作即可登录。

![显示“查看权限”页的屏幕截图](./media/what-is-b2b/consentscreen.png)


## <a name="use-policies-to-securely-share-your-apps-and-services"></a>使用策略安全地共享你的应用和服务

可以使用授权策略保护企业内容。 可在以下级别强制执行多重身份验证等条件访问策略：

- 租户级别。
- 应用程序级别。
- 针对特定来宾用户，保护企业应用和数据。

![显示“条件访问”选项的屏幕截图](./media/what-is-b2b/tutorial-mfa-policy-2.png)



## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>自定义 B2B 来宾用户的载入体验

使用按组织需求自定义的方法引入外部合作伙伴。

- 使用 [B2B 协作邀请 API](https://docs.microsoft.com/graph/api/resources/invitation) 自定义载入体验。

## <a name="next-steps"></a>后续步骤

- [外部标识定价](external-identities-pricing.md)
- [在门户中添加 B2B 协作来宾用户](add-users-administrator.md)
- [了解邀请兑换过程](redemption-experience.md)

