---
title: Azure Active Directory B2C 中的旧用户流版本 | Microsoft Docs
description: 了解 Azure Active Directory B2C 中可用的旧版用户流。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/18/2021
ms.author: v-junlch
ms.subservice: B2C
ms.openlocfilehash: 2a202655c15532a80d60080f6823afeb7193b833
ms.sourcegitcommit: 292892336fc77da4d98d0a78d4627855576922c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2021
ms.locfileid: "98570609"
---
# <a name="legacy-user-flow-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的旧用户流版本

> [!IMPORTANT]
> 本文介绍适用于用户流的旧版本控制方法，该方法提供 V1（生产就绪）版以及 V1.1 和 V2 版（预览版）用户流。 除 Azure 公有云以外的其他环境将继续使用这种旧的版本控制方法。 在 Azure 公有云中，此方法被 [新 **推荐的** **预览** 版](user-flow-versions.md)代替。
> 
Azure Active Directory B2C (Azure AD B2C) 中的用户流可帮助设置完全描述客户标识体验的常见[策略](user-flow-overview.md)。 这些体验包括注册、登录、密码重置和配置文件编辑。

在下表中，除非用户流被标识为“推荐”，否则将其视为“预览版”。 仅应对生产应用程序使用建议的用户流。

## <a name="v1"></a>V1

| 用户流 | 建议 | 描述 |
| --------- | ----------- | ----------- |
| 密码重置 | 是 | 允许用户在验证电子邮件后选择新密码。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>令牌兼容性设置</li><li>[密码复杂性要求](password-complexity.md)</li></ul> |
| 配置文件编辑 | 是 | 允许用户配置用户特性。 使用此用户流，可配置： <ul><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li><li>会话行为</li></ul> |
| 使用 ROPC 登录 | 否 | 允许具有本地帐户的用户直接在本机应用程序中登录（无需浏览器）。 使用此用户流，可配置： <ul><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li></ul> |
| 登录 | 否 | 允许用户登录帐户。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li><li>会话行为</li><li>阻止登录</li><li>强制执行密码重置</li><li>使我保持登录状态 (KMSI)</ul><br>无法使用此用户流自定义用户界面。 |
| 注册 | 否 | 允许用户创建账户。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li><li>会话行为</li><li>[密码复杂性要求](password-complexity.md)</li></ul> |
| 注册和登录 | 是 | 允许用户创建帐户或登录帐户。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li><li>会话行为</li><li>[密码复杂性要求](password-complexity.md)</li></ul>|

## <a name="v11"></a>V1.1

| 用户流 | 建议 | 描述 |
| --------- | ----------- | ----------- |
| 密码重置 v1.1 | 否 | 允许用户在验证电子邮件地址后选择新密码（新页面布局已推出）。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>令牌兼容性设置</li><li>[密码复杂性要求](password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| 用户流 | 建议 | 描述 |
| --------- | ----------- | ----------- |
| 密码重置 v2 | 否 | 允许用户在验证电子邮件后选择新密码。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>令牌兼容性设置</li><li>[年龄限制](basic-age-gating.md)</li><li>[密码复杂性要求](password-complexity.md)</li></ul> |
| 配置文件编辑 v2 | 是 | 允许用户配置用户特性。 使用此用户流，可配置： <ul><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li><li>会话行为</li></ul> |
| 登录 v2 | 否 | 允许用户登录帐户。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li><li>会话行为</li><li>[年龄限制](basic-age-gating.md)</li><li>登录页自定义</li></ul> |
| 注册 v2 | 否 | 允许用户创建账户。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li><li>会话行为</li><li>[年龄限制](basic-age-gating.md)</li><li>[密码复杂性要求](password-complexity.md)</li></ul> |
| 注册和登录 v2 | 否 | 允许用户创建帐户或登录帐户。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>[年龄限制](basic-age-gating.md)</li><li>[密码复杂性要求](password-complexity.md)</li></ul> |

