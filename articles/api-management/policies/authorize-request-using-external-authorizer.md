---
title: 示例 API 管理策略 - 使用外部授权程序授权请求
titleSuffix: Azure API Management
description: Azure API 管理策略示例 - 展示了如何使用封装自定义或旧身份验证/授权逻辑的外部授权程序授权请求。
services: api-management
documentationcenter: ''
author: Johnnytechn
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
origin.date: 06/06/2018
ms.date: 11/18/2020
ms.author: v-johya
ms.openlocfilehash: b0ce9e79c453907d157738d128b79a879113fb60
ms.sourcegitcommit: f1d0f81918b8c6fca25a125c17ddb80c3a7eda7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2020
ms.locfileid: "96306465"
---
# <a name="authorize-requests-using-external-authorizer"></a>使用外部授权程序授权请求

本文中的 Azure API 管理策略示例展示了如何使用封装自定义身份验证/授权逻辑的外部授权程序来保护 API 访问。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-reference.md)。

## <a name="policy"></a>策略

将代码粘贴到“入站”块中  。

```XML
<!-- The policy defined in this file shows how to secure API access by using an external authorizer encapsulating custom authentication/authorization logic.

     In this sample we assume that:

        External authorizer evaluates only the information contained within the Authorization header. Alternatively, for example, a full copy of the incoming request can be forwarded to the authorizer by setting "mode" to copy in the send-request policy.

        External authorizer URL is stored in a named value called "authorizer-url" and is secured with a key included in a query parameter.

        External authorizer responds with a JSON object containing a property called "status" that is set to 200 if authorization was successful and 403 if it wasn't.
-->

<!-- Copy the following snippet into the inbound section and look at the trace window to see it work. -->

<policies>
    <inbound>
        <base/>
        <!-- Ensure presence of Authorization header -->
        <choose>
            <when condition="@(!context.Request.Headers.ContainsKey("Authorization"))">
                <return-response>
                    <set-status code="401" reason="Unauthorized" />
                    <set-header name="WWW-Authenticate" exists-action="append">
                        <value>@("Bearer realm="+context.Request.OriginalUrl.Host)</value>
                    </set-header>
                </return-response>
            </when>
        </choose>
        <!-- Check for cached authorization status for the subject -->
        <cache-lookup-value key="@(context.Request.Headers.GetValueOrDefault("Authorization"))" variable-name="status"/>
        <choose>
            <!--
                If a cache miss call external authorizer
            -->
            <when condition="@(!context.Variables.ContainsKey("status"))">
                <!-- Invoke -->
                <send-request mode="new" response-variable-name="response" timeout="10" ignore-error="false">
                    <set-url>{{authorizer-url}}</set-url>
                    <set-method>GET</set-method>
                    <set-header name="Authorization" exists-action="override">
                        <value>@(context.Request.Headers.GetValueOrDefault("Authorization"))</value>
                    </set-header>
                </send-request>
                <!-- Extract authorization status from authorizer's response -->
                <set-variable name="status" value="@(((IResponse)context.Variables["response"]).Body.As<JObject>()["status"].ToString())"/>
                <!-- Cache authorization result -->
                <cache-store-value key="@(context.Request.Headers.GetValueOrDefault("Authorization"))" value="@((string)context.Variables["status"])" duration="5"/>
            </when>
        </choose>
        <!-- Authorize the request -->
        <choose>
            <when condition="@((string)context.Variables["status"] == "403")">
                <return-response>
                    <set-status code="403" reason="Forbidden" />
                </return-response>
            </when>
        </choose>
    </inbound>
    <backend>
        <base/>
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>
```

## <a name="next-steps"></a>后续步骤

了解有关 APIM 策略的详细信息：

+ [访问限制策略](../api-management-access-restriction-policies.md)
+ [策略示例](../policy-reference.md)

