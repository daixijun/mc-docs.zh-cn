---
title: 处理 MSAL4J 中的错误和异常
titleSuffix: Microsoft identity platform
description: 了解如何处理 MSAL4J 应用程序中的错误和异常、条件访问声明质询以及重试。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/05/2021
ms.author: v-junlch
ms.reviewer: saeeda, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 2fe7362df5ee193c5a0523339bc91d9013ee6628
ms.sourcegitcommit: dfdb65cef6a6b089992644f075b9c3f444cb8e36
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98793968"
---
# <a name="handle-errors-and-exceptions-in-msal-for-java"></a>处理 MSAL for Java 中的错误和异常

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-java"></a>MSAL for Java 中的错误处理

在 Java MSAL 中，有三种类型的异常：`MsalClientException`、`MsalServiceException` 和 `MsalInteractionRequiredException`；所有这些异常继承自 `MsalException`。

- 当库或设备本地发生错误时，将引发 `MsalClientException`。
- 当安全令牌服务 (STS) 返回错误响应或发生另一个网络错误时，将引发 `MsalServiceException`。
- 当需要 UI 交互才能成功完成身份验证时，将引发 `MsalInteractionRequiredException`。

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` 公开在对 STS 的请求中返回的 HTTP 标头。 可通过 `MsalServiceException.headers()` 访问这些标头

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

调用 `AcquireTokenSilently()` 时，从适用于 Java 的 MSAL 返回的一个常见状态代码是 `InvalidGrantError`。 这意味着，只有在完成额外的用户交互之后，才能颁发身份验证令牌。 应用程序应再次调用身份验证库，但要在交互模式下为公共客户端应用程序发送 `AuthorizationCodeParameters` 或 `DeviceCodeParameters`。

在大多数情况下，`AcquireTokenSilently` 失败的原因是令牌缓存中没有与请求匹配的令牌。 访问令牌将在 1 小时后过期，`AcquireTokenSilently` 会尝试基于刷新令牌获取新令牌。 在 OAuth2 中，这称为“刷新令牌”流。 此流也可能出于各种原因（例如，当租户管理员配置了更严格的登录策略时）而失败。

导致此错误的某些状况可让用户轻松解决。 例如，他们可能需要接受使用条款，或者无法使用当前配置来实现请求，因为计算机需要连接到特定的企业网络。

MSAL 公开一个 `reason` 字段，使用它可以提供更好的用户体验。 例如，使用 `reason` 字段可以告知用户其密码已过期，或者他们需要许可使用某些资源。 支持的值是 `InteractionRequiredExceptionReason` 枚举的一部分：

| Reason | 含义 | 建议的处理方式 |
|---------|-----------|-----------------------------|
| `BasicAction` | 在交互式身份验证流期间，可通过用户交互来解决条件。 | 使用交互式参数调用 `acquireToken`。 |
| `AdditionalAction` | 在交互式身份验证流以外，可以通过与系统进行附加的补救交互来解决状况。 | 结合交互式参数调用 `acquireToken` 可显示一条解释补救措施的消息。 如果用户不太可能完成补救措施，调用方应用可以选择隐藏需要额外措施的流。 |
| `MessageOnly` | 条件目前无法解决。 启动交互式身份验证流可显示一条解释该状况的消息。 | 结合交互式参数调用 `acquireToken` 可显示一条解释状况的消息。 在用户读取消息并关闭窗口后，`acquireToken` 将返回 `UserCanceled` 错误。 如果该消息不太可能会为用户带来帮助，该应用可以选择隐藏生成消息的流。 |
| `ConsentRequired`| 用户许可缺失或已撤销。 |结合交互式参数调用 `acquireToken`，使用户能够授予许可。 |
| `UserPasswordExpired` | 用户的密码已过期。 | 使用交互式参数调用 `acquireToken`，使用户能够重置其密码。 |
| `None` |  将提供更多详细信息。 在交互式身份验证流期间，可通过用户交互来解决状况。 | 使用交互式参数调用 `acquireToken`。 |

### <a name="code-example"></a>代码示例

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>后续步骤

请考虑[在 MSAL for Java 中启用日志记录](msal-logging.md?tabs=java)，以帮助你诊断和调试问题。

