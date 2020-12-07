---
title: 什么是 Azure Active Directory B2C？
description: 了解如何使用 Azure Active Directory B2C 在应用程序中支持外部标识。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2020
ms.author: v-junlch
ms.subservice: B2C
ms.openlocfilehash: e91ca1fd91652b7e60e6d5a6b1bbc47011e69fae
ms.sourcegitcommit: a1f565fd202c1b9fd8c74f814baa499bbb4ed4a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96507273"
---
# <a name="what-is-azure-active-directory-b2c"></a>什么是 Azure Active Directory B2C？

Azure Active Directory B2C 以服务的形式提供企业到客户的标识。 客户使用其首选的社交、企业或本地帐户标识对应用程序和 API 进行单一登录访问。

![Azure AD B2C 标识提供者和下游应用程序的信息图](./media/overview/azureadb2c-overview.png)

Azure Active Directory B2C (Azure AD B2C) 是一个客户标识访问管理 (CIAM) 解决方案，每天能够支持数百万用户和数十亿次身份验证。 它负责处理身份验证平台的规模和安全性，并监视和自动应对拒绝服务、密码喷洒或暴力攻击等威胁。

## <a name="custom-branded-identity-solution"></a>自定义品牌的标识解决方案

Azure AD B2C 是一种贴牌式身份验证解决方案。 你可以使用自己的品牌自定义整个用户体验，使其能够与 Web 和移动应用程序无缝融合。

可以自定义当用户注册、登录和修改其个人资料信息时 Azure AD B2C 显示的每一页。 可以自定义用户旅程中的 HTML、CSS 和 JavaScript，使 Azure AD B2C 体验的外观类似于应用程序的原生组成部分。

## <a name="single-sign-on-access-with-a-user-provided-identity"></a>使用用户提供的标识进行单一登录访问

Azure AD B2C 使用基于标准的身份验证协议，包括 OpenID Connect、OAuth 2.0 和 SAML。 它与大多数新式应用程序和商用现货软件相集成。

![第三方标识与 Azure AD B2C 联合的示意图](./media/overview/scenario-singlesignon.png)

Azure AD B2C 充当 Web 应用程序、移动应用和 API 的中心身份验证机构，使你能够为所有这些应用构建单一登录 (SSO) 解决方案。 集中收集用户个人资料和偏好信息，并捕获有关登录行为和注册转换的详细分析。

## <a name="integrate-with-external-user-stores"></a>与外部用户存储集成

Azure AD B2C 提供一个目录，其中可以保存每个用户的 100 个自定义属性。 但是，你也可以与外部系统相集成。 例如，使用 Azure AD B2C 进行身份验证，但将权限委托给用作客户数据真实来源的外部客户关系管理 (CRM) 或客户忠诚度数据库。

另一种外部用户存储方案是让 Azure AD B2C 处理应用程序的身份验证，但与存储用户个人资料或个人数据的外部系统相集成。 例如，满足区域或本地数据存储策略规定的数据驻留要求。

![与外部用户存储通信的 Azure AD B2C 的逻辑关系图](./media/overview/scenario-remoteprofile.png)

Azure AD B2C 有助于在注册或编辑个人资料过程中从用户收集信息，然后将该数据转交到外部系统。 在将来的身份验证过程中，Azure AD B2C 可以从外部系统检索数据，并根据需要将此数据包含为发送到应用程序的身份验证令牌响应的一部分。

## <a name="progressive-profiling"></a>渐进式分析

另一个用户旅程选项包括渐进式分析。 渐进式分析可让客户通过收集极少量的信息来快速完成第一个事务。 今后在客户登录时，将以渐进方式从客户收集更多的个人资料数据。

![渐进式分析的直观描绘](./media/overview/scenario-progressive.png)

## <a name="third-party-identity-verification-and-proofing"></a>第三方标识验证和证明

使用 Azure AD B2C 可以收集用户数据，然后将其传递给第三方系统来执行验证、信任评分和审批用户帐户创建操作，以此帮助完成标识验证和证明。

![显示第三方标识证明用户流的示意图](./media/overview/scenario-idproofing.png)

这仅仅是用作企业到客户标识平台的 Azure AD B2C 的一部分功能。 本概述文章的以下部分将演练一个使用 Azure AD B2C 的演示应用程序。 另外，也欢迎你直接查看更深入的文章 [Azure AD B2C 技术概述](technical-overview.md)。

## <a name="next-steps"></a>后续步骤

大致了解 Azure AD B2C 及其可以帮助实现的某些方案后，接下来请更深入地了解其功能和技术方面。

> [!div class="nextstepaction"]
> [Azure AD B2C 技术概述 >](technical-overview.md)

<!-- LINKS - External -->
[woodgrove]: https://aka.ms/ciamdemo
[woodgrove-repo]: https://github.com/Azure-Samples/active-directory-external-identities-woodgrove-demo

