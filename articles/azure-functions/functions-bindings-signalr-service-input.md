---
title: Azure Functions SignalR 服务输入绑定
description: 了解如何在 Azure Functions 中返回 SignalR 服务终结点 URL 和访问令牌。
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 01/04/2021
ms.author: v-junlch
ms.openlocfilehash: 52eca7340117f2571d8c836af2d5a764e153eeed
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98022668"
---
# <a name="signalr-service-input-binding-for-azure-functions"></a>Azure Functions 的 SignalR 服务输入绑定

客户端在连接到 Azure SignalR 服务之前，必须检索服务终结点 URL 和有效的访问令牌。 *SignalRConnectionInfo* 输入绑定生成 SignalR 服务终结点 URL 和有效的令牌，这两者可以用来连接到服务。 由于此令牌有时间限制，并且可以用来对需要连接的特定用户进行身份验证，因此不应缓存此令牌，也不应在客户端之间共享它。 使用此绑定的 HTTP 触发器可供客户端用来检索连接信息。

若要详细了解如何使用此绑定来创建一个可以由 SignalR 客户端 SDK 使用的“协商”函数，请参阅 SignalR 服务概念文档中的[“Azure Functions 开发和配置”一文](../azure-signalr/signalr-concept-serverless-development-config.md)。

有关设置和配置详细信息，请参阅[概述](functions-bindings-signalr-service.md)。

## <a name="example"></a>示例

# <a name="c"></a>[C#](#tab/csharp)

以下示例演示了一个 [C# 函数](functions-dotnet-class-library.md)，该函数使用输入绑定获取 SignalR 连接信息，并通过 HTTP 将其返回。

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

以下示例演示 function.json  文件中的一个 SignalR 连接信息输入绑定，以及使用该绑定来返回连接信息的 [C# Script 函数](functions-reference-csharp.md)。

下面是 *function.json* 文件中的绑定数据：

示例 function.json：

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

C# 脚本代码如下所示：

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下示例演示 *function.json* 文件中的一个 SignalR 连接信息输入绑定，以及使用该绑定来返回连接信息的 [JavaScript 函数](functions-reference-node.md)。

下面是 *function.json* 文件中的绑定数据：

示例 function.json：

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

JavaScript 代码如下所示：

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="java"></a>[Java](#tab/java)

以下示例演示了一个 [Java 函数](functions-reference-java.md)，该函数使用输入绑定获取 SignalR 连接信息，并通过 HTTP 将其返回。

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="authenticated-tokens"></a>已进行身份验证的令牌

如果此函数由经过身份验证的客户端触发，则可向生成的令牌添加用户 ID 声明。 可以轻松地使用[应用服务身份验证](../app-service/overview-authentication-authorization.md)向函数应用添加身份验证。

应用服务身份验证会设置名为 `x-ms-client-principal-id` 和 `x-ms-client-principal-name`（分别包含经身份验证的用户的客户端主体 ID 和名称）的 HTTP 标头。

# <a name="c"></a>[C#](#tab/csharp)

可以使用[绑定表达式](./functions-bindings-expressions-patterns.md)`{headers.x-ms-client-principal-id}` 或 `{headers.x-ms-client-principal-name}` 将绑定的 `UserId` 属性设置为任一标头中的值。

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

可以使用[绑定表达式](./functions-bindings-expressions-patterns.md)`{headers.x-ms-client-principal-id}` 或 `{headers.x-ms-client-principal-name}` 将绑定的 `userId` 属性设置为任一标头中的值。

示例 function.json：

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

C# 脚本代码如下所示：

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

可以使用[绑定表达式](./functions-bindings-expressions-patterns.md)`{headers.x-ms-client-principal-id}` 或 `{headers.x-ms-client-principal-name}` 将绑定的 `userId` 属性设置为任一标头中的值。

示例 function.json：

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

JavaScript 代码如下所示：

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="java"></a>[Java](#tab/java)

可以使用[绑定表达式](./functions-bindings-expressions-patterns.md)`{headers.x-ms-client-principal-id}` 或 `{headers.x-ms-client-principal-name}` 将绑定的 `userId` 属性设置为任一标头中的值。

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="next-steps"></a>后续步骤

- [处理来自 SignalR 服务的消息（触发绑定）](./functions-bindings-signalr-service-trigger.md)
- [发送 SignalR 服务消息（输出绑定）](./functions-bindings-signalr-service-output.md) 

