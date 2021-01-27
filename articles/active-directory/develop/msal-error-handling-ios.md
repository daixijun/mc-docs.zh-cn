---
title: 处理 MSAL for iOS/macOS 中的错误和异常
titleSuffix: Microsoft identity platform
description: 了解如何处理 MSAL for iOS/macOS 应用程序中的错误和异常、条件访问声明质询以及重试。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/05/2021
ms.author: v-junlch
ms.reviewer: saeeda, oldalton
ms.custom: aaddev
ms.openlocfilehash: 5bfa39878ecd16e9c3c36454349e80dfb9e030f1
ms.sourcegitcommit: dfdb65cef6a6b089992644f075b9c3f444cb8e36
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98793973"
---
# <a name="handle-errors-and-exceptions-in-msal-for-iosmacos"></a>处理 MSAL for iOS/macOS 中的错误和异常

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-iosmacos"></a>MSAL for iOS/macOS 中的错误处理

[MSALError 枚举](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128)中列出了适用于 iOS 和 macOS 的 MSAL 错误的完整列表。

生成的所有 MSAL 错误都以 `MSALErrorDomain` 域返回。

对于系统错误，MSAL 将从系统 API 返回原始 `NSError`。 例如，如果令牌获取因未建立网络连接而失败，则 MSAL 将返回错误，同时返回 `NSURLErrorDomain` 域和 `NSURLErrorNotConnectedToInternet` 代码。

建议至少在客户端处理以下两个 MSAL 错误：

- `MSALErrorInteractionRequired`：用户必须执行交互式请求。 有许多状况可能会导致此错误，例如，身份验证会话过期，或需要满足额外的身份验证要求。 调用 MSAL 交互式令牌获取 API 可予以恢复。 

- `MSALErrorServerDeclinedScopes`：部分或所有范围被拒绝。 确定是仅使用授予的范围继续，还是停止登录过程。

> [!NOTE]
> 请只将 `MSALInternalError` 枚举用于参考和调试。 请勿尝试在运行时自动处理这些错误。 如果应用遇到属于 `MSALInternalError` 类别的任何错误，可以显示一条面向用户的常规消息来解释发生了什么情况。

例如，`MSALInternalErrorBrokerResponseNotReceived` 表示用户未完成身份验证并已手动返回到应用。 在这种情况下，应用应该显示一条常规错误消息来指出身份验证未完成，并建议他们尝试再次进行身份验证。

以下 Objective-C 示例代码演示了处理某些常见错误状况的最佳做法。

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                                        
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

适用于 iOS 和 macOS 的 MSAL 允许你在交互式和无提示令牌获取方案中请求特定声明。

若要请求自定义声明，请在 `MSALSilentTokenParameters` 或 `MSALInteractiveTokenParameters`中指定 `claimsRequest`。

有关详细信息，请参阅[使用适用于 iOS 和 macOS 的 MSAL 请求自定义声明](request-custom-claims.md)。

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>后续步骤

请考虑[在 MSAL iOS/macOS 中启用日志记录](msal-logging.md?tabs=swift)，以帮助你诊断和调试问题。

