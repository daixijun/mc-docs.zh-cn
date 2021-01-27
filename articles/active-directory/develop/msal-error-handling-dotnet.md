---
title: 处理 MSAL.NET 中的错误和异常
titleSuffix: Microsoft identity platform
description: 了解如何处理 MSAL.NET 中的错误和异常、条件访问声明质询以及重试。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/05/2021
ms.author: v-junlch
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 385108c14bfdca1644abc1969dfb5c25fbf19a72
ms.sourcegitcommit: dfdb65cef6a6b089992644f075b9c3f444cb8e36
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98793975"
---
# <a name="handle-errors-and-exceptions-in-msalnet"></a>处理 MSAL.NET 中的错误和异常

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msalnet"></a>MSAL.NET 中的错误处理

处理 .NET 异常时，可以使用异常类型本身和 `ErrorCode` 成员来区分不同的异常。 `ErrorCode` 的值是 [MsalError](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.msalerror) 类型的常量。

也可以查看 [MsalClientException](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.msalexception)、[MsalServiceException](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.msalserviceexception) 和 [MsalUIRequiredException](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.msaluirequiredexception) 的字段。

如果引发 [MsalServiceException](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.msalserviceexception)，请尝试查看[身份验证和授权错误代码](reference-aadsts-error-codes.md)，以查看其中是否列出了相关代码。

### <a name="common-net-exceptions"></a>常见的 .NET 异常

下面是可能引发的常见异常和一些可能的缓解措施：  

| 异常 | 错误代码 | 缓解措施|
| --- | --- | --- |
| [MsalUiRequiredException](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS65001：用户或管理员尚未许可使用名为“{appName}”、ID 为“{appId}”的应用程序。 针对此用户和资源发送交互式授权请求。| 需要先获取用户的许可。 如果未使用 .NET Core（它没有任何 Web UI），请调用 `AcquireTokeninteractive`（仅一次）。 如果使用 .NET Core 或者不希望执行 `AcquireTokenInteractive`，则用户可以导航到某个 URL 来提供许可：`https://login.partner.microsoftonline.cn/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`。 若要调用 `AcquireTokenInteractive`，请使用 `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS50079：用户必须使用[多重身份验证 (MFA)](../authentication/concept-mfa-howitworks.md)。| 无缓解措施。 如果为租户配置了 MFA 并且 Azure Active Directory (AAD) 决定强制实施 MFA，则需要回退到 `AcquireTokenInteractive` 或 `AcquireTokenByDeviceCode` 等交互式流。|
| [MsalServiceException](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.msalserviceexception) |AADSTS90010：不支持通过 */common* 或 */consumers* 终结点的授予类型。 请使用 */organizations* 或特定于租户的终结点。 使用了 */common*。| 根据 Azure AD 发出的消息中所述，颁发机构需要使用一个租户或 */organizations*。|
| [MsalServiceException](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.msalserviceexception) | AADSTS70002：请求正文必须包含以下参数：`client_secret or client_assertion`。| 如果应用程序未注册为 Azure AD 中的公共客户端应用程序，则可能会引发此异常。 在 Azure 门户中编辑应用程序的清单，并将 `allowPublicClient` 设置为 `true`。 |
| [MsalClientException](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.msalclientexception)| `unknown_user Message`：无法识别已登录的用户| 库无法查询当前的 Windows 已登录用户，或者此用户未加入 AD 或 Azure AD（已加入工作区的用户不受支持）。 缓解措施 1：在 UWP 中，检查应用程序是否具有以下功能：企业身份验证、专用网络（客户端和服务器）、用户帐户信息。 缓解措施 2：实现自己的逻辑以提取用户名（例如 john@contoso.com），并使用 `AcquireTokenByIntegratedWindowsAuth` 表单来提取用户名。|
| [MsalClientException](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.msalclientexception)|integrated_windows_auth_not_supported_managed_user| 此方法依赖于 Active Directory (AD) 公开的协议。 如果在 Azure AD 中创建了一个用户但该用户不受 AD 的支持（“托管”用户），则此方法将会失败。 在 AD 中创建的受 Azure AD 支持的用户（“联合”用户）可以受益于这种非交互式身份验证方法。 缓解措施：使用交互式身份验证。|

### `MsalUiRequiredException`

调用 `AcquireTokenSilent()` 时，从 MSAL.NET 返回的一个常见状态代码是 `MsalError.InvalidGrantError`。 此状态代码表示应用程序应再次调用身份验证库，但要在交互模式下调用（用于公共客户端应用程序的 AcquireTokenInteractive 或 AcquireTokenByDeviceCodeFlow 会在 Web 应用中执行质询）。 这是因为，只有在完成额外的用户交互之后，才能颁发身份验证令牌。

在大多数情况下，`AcquireTokenSilent` 失败的原因是令牌缓存中没有与请求匹配的令牌。 访问令牌将在 1 小时后过期，`AcquireTokenSilent` 会尝试基于刷新令牌获取新令牌（在 OAuth2 中，这称为“刷新令牌”流）。 此流也可能出于各种原因（例如，租户管理员配置了更严格的登录策略）而失败。 

交互的目的是让用户采取某种措施。 在这些条件中，有些对于用户来说很容易解决（例如，通过单击接受使用条款），而有些则无法通过当前配置进行解决（例如，有问题的计算机需要连接到特定的公司网络）。 有些条件可帮助用户设置多重身份验证，或者在其设备上安装 Microsoft Authenticator。

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` 分类枚举

MSAL 公开一个 `Classification` 字段，你可以读取该字段，以便提供更好的用户体验。 例如，用于告知用户其密码已过期，或者他们需要许可使用某些资源。 支持的值是 `UiRequiredExceptionClassification` 枚举的一部分：

| 分类    | 含义           | 建议的处理方式 |
|-------------------|-------------------|----------------------|
| BasicAction | 在交互式身份验证流期间，可通过用户交互来解决条件。 | 调用 AcquireTokenInteractively()。 |
| AdditionalAction | 在交互式身份验证流以外，可以通过与系统进行附加的补救交互来解决条件。 | 调用 AcquireTokenInteractively() 以显示一条解释补救措施的消息。 如果用户不太可能完成补救措施，调用方应用程序可以选择隐藏需要 additional_action 的流。 |
| MessageOnly      | 目前无法解决条件。 启动交互式身份验证流会显示一条解释该条件的消息。 | 调用 AcquireTokenInteractively() 以显示一条解释该条件的消息。 用户阅读该消息并关闭窗口后，AcquireTokenInteractively() 将返回 UserCanceled 错误。 如果该消息不太可能会为用户带来帮助，调用方应用程序可以选择隐藏导致 message_only 的流。|
| ConsentRequired  | 用户许可缺失或已撤销。 | 调用 AcquireTokenInteractively()，让用户提供许可。 |
| UserPasswordExpired | 用户的密码已过期。 | 调用 AcquireTokenInteractively()，使用户能够重置其密码。 |
| PromptNeverFailed| 已结合参数 prompt=never 调用交互式身份验证，这会强制 MSAL 依赖于浏览器 Cookie，且不显示浏览器。 此操作已失败。 | 调用 AcquireTokenInteractively() 但不指定 Prompt.None |
| AcquireTokenSilentFailed | MSAL SDK 没有足够的信息，无法从缓存中提取令牌。 原因可能是缓存中没有令牌，或找不到帐户。 错误消息中提供了更多详细信息。  | 调用 AcquireTokenInteractively()。 |
| 无    | 不提供更多详细信息。 在交互式身份验证流期间，可通过用户交互来解决条件。 | 调用 AcquireTokenInteractively()。 |

## <a name="net-code-example"></a>.NET 代码示例

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```
[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

从 MSAL.NET 调用需要条件访问的 API 时，应用程序需要处理声明质询异常。 此错误将显示为 [MsalServiceException](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.msalserviceexception)，其中的 [Claims](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.msalserviceexception.claims) 属性不为空。

若要处理声明质询，需要使用 `PublicClientApplicationBuilder` 类的 `.WithClaim()` 方法。

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

### <a name="http-error-codes-500-600"></a>HTTP 错误代码 500-600

对于 HTTP 错误代码为 500-600 的错误，MSAL.NET 实现一个简单的重试一次机制。

[MsalServiceException](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.msalserviceexception) 以 `namedHeaders` 属性的形式公开 `System.Net.Http.Headers.HttpResponseHeaders`。 可以使用错误代码中的附加信息来提高应用程序的可靠性。 对于前面所述的场景，可以使用 `RetryAfterproperty`（类型为 `RetryConditionHeaderValue`）并计算重试时间。

下面是使用客户端凭据流的守护程序应用程序示例。 可以根据用于获取令牌的方法改编此示例。

```csharp

bool retry = false;
do
{
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account).ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (serviceException.ErrorCode == "temporarily_unavailable")
         {
             RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
             if (retryAfter.Delta.HasValue)
             {
                 delay = retryAfter.Delta;
             }
             else if (retryAfter.Date.HasValue)
             {
                 delay = retryAfter.Date.Value.Offset;
             }
         }
    }
    // . . .
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```

## <a name="next-steps"></a>后续步骤

请考虑[在 MSAL.NET 中启用日志记录](msal-logging.md?tabs=dotnet)，以帮助你诊断和调试问题。

