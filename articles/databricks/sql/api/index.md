---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 11/17/2020
title: SQL Analytics API 参考 - Azure Databricks
description: 了解 Azure Databricks SQL Analytics REST API 支持的服务。
ms.openlocfilehash: ce4ebcab2be186f4224ecd7f0fa31cb6be77b331
ms.sourcegitcommit: bb7497d5a11e8fb506907221ff65a18e6c523372
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98692328"
---
# <a name="sql-analytics-api-reference"></a>SQL Analytics API 参考

> [!IMPORTANT]
>
> 此功能目前以[公共预览版](../../release-notes/release-types.md)提供。 请联系 Azure Databricks 代表，以申请访问权限。

Databricks SQL Analytics REST API 支持用于管理 SQL 终结点和查询历史记录的服务。

本文概述了如何使用 REST API。 本文末尾列出了各个 API 参考的链接。

若要了解如何使用个人访问令牌向 REST API 进行身份验证，请参阅[使用 Azure Databricks 个人访问令牌进行身份验证](authentication.md)。

若要了解如何使用 Azure Active Directory 令牌向 REST API 进行身份验证，请参阅[使用 Azure Active Directory 令牌进行身份验证](../../dev-tools/api/latest/aad/index.md)。 有关示例，请参阅[使用用户的 Azure AD 访问令牌](../../dev-tools/api/latest/aad/app-aad-token.md#use-token)和[使用服务主体的 AAD 访问令牌](../../dev-tools/api/latest/aad/service-prin-aad-token.md#use-token)。

## <a name="rate-limits"></a>速率限制

Databricks REST API 支持每个工作区最多 30 个请求/秒。 超过速率限制的请求会出现 [429 响应状态代码](https://developer.mozilla.org/docs/Web/HTTP/Status/429)。

## <a name="parse-output"></a>分析输出

分析 JSON 输出的各个部分可能很有用。 在这种情况下，建议使用实用工具 ``jq``。 有关详细信息，请参阅 [jq 手册](https://stedolan.github.io/jq/manual/)。 可以通过运行 ``brew install jq`` 来使用 Homebrew 在 MacOS 上安装 ``jq``。

某些 ``STRING`` 字段（其中包含供 UI 使用的错误/描述性消息）未结构化，在编程工作流中请不要依赖这些字段的格式。

## <a name="invoke-a-get-using-a-query-string"></a>使用查询字符串来调用 GET

尽管大多数 API 调用都要求必须指定 JSON 正文，但对于 ``GET`` 调用，可以指定查询字符串。

若要获取 SQL 终结点的详细信息，请运行：

```bash
curl ... https://<databricks-instance>/api/2.0/sql/endpoints/get?id=<endpoint-id>
```

## <a name="apis"></a>API

* [SQL 终结点 API](sql-endpoints.md)
* [查询历史记录 API](query-history.md)
* [使用 Azure Databricks 个人访问令牌进行身份验证](authentication.md)