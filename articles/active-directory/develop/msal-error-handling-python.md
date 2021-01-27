---
title: 处理 MSAL for Python 中的错误和异常
titleSuffix: Microsoft identity platform
description: 了解如何处理 MSAL for Python 应用程序中的错误和异常、条件访问声明质询以及重试。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/05/2021
ms.author: v-junlch
ms.reviewer: saeeda, rayluo
ms.custom: aaddev
ms.openlocfilehash: 2b62b655ecb83a923ac0acbdac746c55ae0572d6
ms.sourcegitcommit: dfdb65cef6a6b089992644f075b9c3f444cb8e36
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98793972"
---
# <a name="handle-errors-and-exceptions-in-msal-for-python"></a>处理 MSAL for Python 中的错误和异常

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-python"></a>MSAL for Python 中的错误处理

在 MSAL for Python 中，大多数错误都作为 API 调用的返回值传达。 此错误以字典形式表示，其中包含来自 Microsoft 标识平台的 JSON 响应。

* 成功的响应包含 `"access_token"` 键。 响应的格式通过 OAuth2 协议定义。 有关详细信息，请参阅 [5.1 成功响应](https://tools.ietf.org/html/rfc6749#section-5.1)
* 错误响应包含 `"error"`，并且通常包含 `"error_description"`。 响应的格式通过 OAuth2 协议定义。 有关详细信息，请参阅 [5.2 错误响应](https://tools.ietf.org/html/rfc6749#section-5.2)

返回错误时，`"error_description"` 键包含用户可读的消息，而该消息通常又包含 Microsoft 标识平台错误代码。 有关各种错误代码的详细信息，请参阅[身份验证和授权错误代码](./reference-aadsts-error-codes.md)。

在 MSAL for Python 中，异常很罕见，因为系统对大多数错误的处理方式是返回错误值。 仅当你尝试使用该库的方式存在问题时（例如在 API 参数格式不正确时），才会引发 `ValueError` 异常。

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>后续步骤

请考虑[在 MSAL for Python 中启用日志记录](msal-logging.md?tabs=python)，以帮助你诊断和调试问题。

