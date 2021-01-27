---
author: mmacy
ms.author: v-junlch
ms.date: 01/05/2021
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 85fe3db95b992bc169d3833a21ab27bee29cc9c9
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98793967"
---
本文概述不同类型的错误，并提供有关处理常见登录错误的建议。

## <a name="msal-error-handling-basics"></a>MSAL 错误处理基础知识

Microsoft 身份验证库 (MSAL) 中的异常旨在帮助应用开发人员进行故障排除，而不是用于显示给最终用户。 异常消息未经本地化。

处理异常和错误时，可以使用异常类型本身和错误代码来区分不同的异常。  有关错误代码的列表，请参阅 [Azure AD 身份验证和授权错误代码](../articles/active-directory/develop/reference-aadsts-error-codes.md)。

在登录体验期间，可能会遇到有关许可、条件访问（MFA、设备管理、基于位置的限制）、令牌颁发和兑换以及用户属性的错误。

以下部分提供了有关应用的错误处理的更多详细信息。

