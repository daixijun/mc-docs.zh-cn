---
title: 使用 Azure AD B2C 构建可复原的最终用户体验 | Microsoft Docs
description: 使用 Azure AD B2C 在最终用户体验中构建复原能力的方法
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: v-junlch
manager: martinco
ms.reviewer: ''
ms.date: 12/02/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6765ea1e959ce5bd7826a56e6bd3c6bb8d6f9136
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97004308"
---
# <a name="resilient-end-user-experience"></a>可复原的最终用户体验

注册和登录最终用户体验由以下几个元素组成：

- 用户与之交互的界面 - 例如 CSS、HTML 和 JavaScript

- 用户流和你创建的自定义策略 - 例如注册、登录和配置文件编辑

- 应用程序的标识提供者 (IDP) - 例如本地帐户用户名/密码和 Outlook

![图像显示了最终用户体验组件](./media/resilient-end-user-experiences/end-user-experience-architecture.png)

## <a name="choose-between-user-flow-and-custom-policy"></a>在用户流和自定义策略之间进行选择  

为帮助你设置最常见的标识任务，Azure AD B2C 提供了内置的可配置[用户流](/active-directory-b2c/user-flow-overview)。 你还可以构建你自己的[自定义策略](/active-directory-b2c/custom-policy-overview)，这些策略为你提供最大的灵活性。 但是，建议你仅使用自定义策略来处理复杂的情况。

### <a name="how-to-decide-between-user-flow-and-custom-policy"></a>如何在用户流与自定义策略之间做出选择

如果内置用户流可以满足你的业务需求，请选择内置用户流。 由于 Microsoft 进行了广泛的测试，因此你可以最大限度地减少验证这些标识用户流的策略级功能、性能或缩放所需的测试。 你仍需测试应用程序的功能、性能和缩放。

如果你由于业务需求而选择自定义策略，则除了应用程序级测试外，还请确保对功能、性能或缩放执行策略级测试。

请参阅[比较用户流和自定义策略](/active-directory-b2c/custom-policy-overview#comparing-user-flows-and-custom-policies)一文来做出决定。

## <a name="choose-multiple-idps"></a>选择多个 IDP

使用[外部标识提供者](/active-directory-b2c/technical-overview#external-identity-providers)时，请务必在外部提供者变得不可用的情况下使用回退计划。

### <a name="how-to-set-up-multiple-idps"></a>如何设置多个 IDP

在注册外部标识提供者的过程中，请包括已验证的标识声明，例如用户的手机号码或电子邮件地址。 将已验证的声明提交到基础 Azure AD B2C 目录实例。 如果外部提供者不可用，则恢复使用已验证的标识声明，并回退到使用电话号码作为身份验证方法。 另一个选项是向用户发送一次性密码，以允许用户登录。

 请按照以下步骤[生成备用身份验证路径](https://github.com/azure-ad-b2c/samples/tree/master/policies/idps-filter)：

 1. 配置你的注册策略，以允许通过本地帐户和外部 IDP 进行注册。

 2. 配置一个配置文件策略，以允许用户在登录后[将其他标识关联到帐户](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/account-linking)。

 3. 通知并允许用户在中断期间切换到备用 IDP。

## <a name="use-a-content-delivery-network"></a>使用内容分发网络

内容分发网络 (CDN) 具有更好的性能，并且用来存储自定义用户流 UI 比 Blob 存储更便宜。 网页内容将通过地理上分布的高可用性服务器网络更快地分发。  

请定期通过端到端方案和负载测试来测试你的 CDN 的可用性和内容分发性能。 如果为即将到来的流量激增（由于促销或假日流量）进行规划，请修改你用于负载测试的估计值。
  
## <a name="next-steps"></a>后续步骤

- [面向 Azure AD B2C 开发人员的复原能力资源](resilience-b2c.md)
  
  - [与外部进程建立可复原接口](resilient-external-processes.md)
  - [通过开发人员最佳做法实现复原能力](resilience-b2c-developer-best-practices.md)
  - [通过监视和分析实现复原能力](resilience-with-monitoring-alerting.md)
- [在身份验证基础结构中构建复原能力](resilience-in-infrastructure.md)
- [提高应用程序中身份验证和授权的复原能力](resilience-app-development-overview.md)

