---
title: 首选 MSAL | Azure
description: 包含指示最好使用 MSAL 的文件。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: include
ms.date: 01/05/2021
ms.author: v-junlch
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: e066815c74778a1fdccbd9170bd345db61a2a00e
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98021621"
---
本文介绍如何直接针对应用程序中的协议进行编程，以从 Azure AD 请求令牌。  如果可能，建议你改用受支持的 Microsoft 身份验证库 (MSAL) 来[获取令牌并调用受保护的 Web API](../authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。  另请参阅[使用 MSAL 的示例应用](../sample-v2-code.md)。

