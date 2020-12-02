---
title: Azure 应用配置 REST API - 版本控制
description: 使用 Azure 应用配置 REST API 进行版本控制的参考页
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: a6bbb0938ac951bbf43f1b3e2577633cd51243c3
ms.sourcegitcommit: a6aca2f2d1295cd5ed07e38bf9f18f8c345ba409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96231107"
---
# <a name="versioning"></a>版本控制

api-version：1.0

每个客户端请求都必须提供显式 API 版本作为查询字符串参数。 例如： `https://{myconfig}.azconfig.io/kv?api-version=1.0`

`api-version` 以 SemVer (major.minor) 格式表示。 不支持范围或版本协商。

## <a name="error-response"></a>错误响应

下面概述了当请求的 API 版本无法进行匹配时服务器返回的可能的错误响应的摘要。

### <a name="api-version-unspecified"></a>未指定 API 版本

当客户端在未提供 API 版本的情况下发出请求时发生。

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "API version is not specified",
  "name": "api-version",
  "detail": "An API version is required, but was not specified.",
  "status": 400
}
```

### <a name="unsupported-api-version"></a>API 版本不受支持

当客户端请求的 API 版本与服务器所支持的任何 API 版本都不匹配时发生。

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Unsupported API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

### <a name="invalid-api-version"></a>API 版本无效

当客户端使用 API 版本发出请求，但此值格式不正确或无法由服务器分析时发生。

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8  
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

### <a name="ambiguous-api-version"></a>API 版本不明确

当客户端请求对服务器来说不明确的 API 版本时发生。 例如，多个不同的值。

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Ambiguous API version",
  "name": "api-version",
  "detail": "The following API versions were requested: {comma separated api versions}. At most, only a single API version may be specified. Please update the intended API version and retry the request.",
  "status": 400
}
```
