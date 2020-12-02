---
title: Azure 应用程序配置 REST API - 授权
description: 关于使用 Azure 应用程序配置 REST API 进行授权的参考页面
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 9895e672d02e573a9af3cb8d0df4c4de77c349d5
ms.sourcegitcommit: a6aca2f2d1295cd5ed07e38bf9f18f8c345ba409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96231133"
---
# <a name="authorization"></a>授权

授权是指用于确定调用方在发出请求时具有哪些权限的过程。 存在多种授权模型。 用于请求的授权模型由所使用的[身份验证](./rest-api-authentication-index.md)方法决定。 授权模型如下所示。

## <a name="hmac"></a>HMAC

与 HMAC 身份验证关联的[授权模型](./rest-api-authorization-hmac.md)模型将权限划分为“只读”或“读写”。 有关详细信息，请参阅 [HMAC 授权](./rest-api-authorization-hmac.md)页面。

## <a name="azure-active-directory"></a>Azure Active Directory

与 Azure Active Directory (Azure AD) 身份验证关联的[授权模型](./rest-api-authorization-azure-ad.md)使用 Azure RBAC 来控制权限。 有关详细信息，请参阅 [Azure AD 授权](./rest-api-authorization-azure-ad.md)页面。
