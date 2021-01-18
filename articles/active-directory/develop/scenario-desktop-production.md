---
title: 将调用 Web API 的桌面应用移到生产环境 - Microsoft 标识平台 | Azure
description: 了解如何将调用 Web API 的桌面应用移到生产环境
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/06/2021
ms.author: v-junlch
ms.custom: aaddev
ms.openlocfilehash: a5d78d30de99c3c43d921287fe16c67738d5ea74
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98022520"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>用于调用 Web API 的桌面应用：移到生产环境

本文介绍如何将调用 Web API 的桌面应用移到生产环境。

## <a name="handle-errors-in-desktop-applications"></a>在桌面应用程序中处理错误

你已了解，在不同的流中如何处理静默流的错误，如代码片段所示。 你还了解，有些情况下需要交互（与增量许可和条件访问中的情况一样）。

## <a name="have-the-user-consent-upfront-for-several-resources"></a>让用户提前许可多个资源

> [!NOTE]
> 获得多个资源的同意适用于 Microsoft 标识平台，但不适用于 Azure Active Directory (Azure AD) B2C。 Azure AD B2C 仅支持管理员同意，不支持用户同意。

不能使用 Microsoft 标识平台 (v2.0) 终结点一次获取多个资源的令牌。 `scopes` 参数只能包含单个资源的范围。 可以使用 `extraScopesToConsent` 参数确保用户预先同意多个资源。

例如，你可能有两个资源（每个资源有两个范围）：

- `https://mytenant.partner.onmschina.cn/customerapi`，范围为 `customer.read` 和 `customer.write`
- `https://mytenant.partner.onmschina.cn/vendorapi`，范围为 `vendor.read` 和 `vendor.write`

在此示例中，请使用具有 `extraScopesToConsent` 参数的 `.WithAdditionalPromptToConsent` 修饰符。

例如：

### <a name="in-msalnet"></a>在 MSAL.NET 中

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.partner.onmschina.cn/customerapi/customer.read",
  "https://mytenant.partner.onmschina.cn/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.partner.onmschina.cn/vendorapi/vendor.read",
 "https://mytenant.partner.onmschina.cn/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopesToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

### <a name="in-msal-for-ios-and-macos"></a>在适用于 iOS 和 macOS 的 MSAL 中

Objective-C：

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.partner.onmschina.cn/customerapi/customer.read",
                                @"https://mytenant.partner.onmschina.cn/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.partner.onmschina.cn/vendorapi/vendor.read",
                              @"https://mytenant.partner.onmschina.cn/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Swift：

```swift
let scopesForCustomerApi = ["https://mytenant.partner.onmschina.cn/customerapi/customer.read",
                            "https://mytenant.partner.onmschina.cn/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.partner.onmschina.cn/vendorapi/vendor.read",
                          "https://mytenant.partner.onmschina.cn/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

此调用为你获得第一个 Web API 的访问令牌。

如果需要调用第二个 Web API，请调用 `AcquireTokenSilent` API。

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

