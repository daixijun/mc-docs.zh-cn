---
title: 生成调用 Web API 的单页应用 - Microsoft 标识平台 | Azure
description: 了解如何生成调用 Web API 的单页应用程序
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/23/2020
ms.author: v-junlch
ms.custom: aaddev
ms.openlocfilehash: e8d4c6ae9478c677efd1c036f05f6b495c76bb87
ms.sourcegitcommit: 883daddafe881e5f8a9f347df2880064d2375b6d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95918367"
---
# <a name="single-page-application-call-a-web-api"></a>单页应用程序：调用 Web API

我们建议你在调用 Web API 之前调用 `acquireTokenSilent` 方法获取或续订访问令牌。 有了令牌后，就可以调用受保护的 Web API 了。

## <a name="call-a-web-api"></a>调用 Web API

# <a name="javascript"></a>[JavaScript](#tab/javascript)

在 HTTP 请求中使用获得的访问令牌作为持有者令牌来调用任何 Web API，例如 Microsoft Graph API。 例如：

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://microsoftgraph.chinacloudapi.cn/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

# <a name="angular"></a>[Angular](#tab/angular)

MSAL Angular 包装器器利用 HTTP 拦截器自动以无提示方式获取访问令牌并将其附加到对 API 的 HTTP 请求中。 有关详细信息，请参阅[获取令牌以调用 API](scenario-spa-acquire-token.md)。

---

## <a name="next-steps"></a>后续步骤

转到此方案中的下一篇文章：[移到生产环境](scenario-spa-production.md)。

