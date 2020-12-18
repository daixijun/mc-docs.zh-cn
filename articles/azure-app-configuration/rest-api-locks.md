---
title: Azure 应用配置 REST API - 锁
description: 使用 Azure 应用配置 REST API 处理键/值锁的参考页
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 12/14/2020
ms.openlocfilehash: 95689147ebdfc13cbf526f1e18896158cb33f9ee
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97104649"
---
# <a name="locks"></a>锁

此 API（版本 1.0）为键/值资源提供锁定和取消锁定语义。 它支持以下操作：

- 放置锁
- 删除锁

如果存在，`label` 必须是显式标签值（不是通配符）。 对于所有操作，它是一个可选参数。 如果省略，则暗指没有标签。

## <a name="prerequisites"></a>必备条件

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="lock-key-value"></a>锁定键-值

- 必需：``{key}``、``{api-version}``  
- 可选：``label``

```http
PUT /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**响应：**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

如果键/值不存在，则返回以下响应：

```http
HTTP/1.1 404 Not Found
```

## <a name="unlock-key-value"></a>解锁键-值

- 必需：``{key}``、``{api-version}``  
- 可选：``label``

```http
DELETE /locks/{key}?label={label}?api-version={api-version} HTTP/1.1
```

**响应：**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

如果键/值不存在，则返回以下响应：

```http
HTTP/1.1 404 Not Found
```

## <a name="conditional-lock-and-unlock"></a>条件锁定和解锁

若要防止出现争用情况，请使用 `If-Match` 或 `If-None-Match` 请求标头。 `etag` 参数是键表示形式的一部分。 如果省略 `If-Match` 或 `If-None-Match`，则该操作是无条件的。

以下请求仅在当前键/值表示形式与指定的 `etag` 匹配时才应用操作：

```http
PUT|DELETE /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

以下请求仅在当前键/值表示形式存在但与指定的 `etag` 不匹配时才应用操作：

```http
PUT|DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```
