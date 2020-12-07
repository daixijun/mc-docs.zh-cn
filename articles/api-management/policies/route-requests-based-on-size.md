---
title: 示例 API 管理策略 - 基于消息正文大小路由请求
titleSuffix: Azure API Management
description: Azure API 管理策略示例 - 演示如何基于请求的正文大小路由请求。
services: api-management
documentationcenter: ''
author: Johnnytechn
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
origin.date: 10/13/2017
ms.date: 11/18/2020
ms.author: v-johya
ms.openlocfilehash: 368a5e0761200b4a395e2225a6c0e311fa0fbb74
ms.sourcegitcommit: f1d0f81918b8c6fca25a125c17ddb80c3a7eda7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2020
ms.locfileid: "96306400"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>基于请求的正文大小路由请求

本文介绍 Azure API 管理策略示例，该示例演示如何基于请求的正文大小路由请求。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-reference.md)。

## <a name="policy"></a>策略

将代码粘贴到“入站”块中  。

```xml
<!-- The policy defined in this file demonstrates how to route requests based on the size of the message body. -->
<!-- Content-Length header contains the size of the message body. -->

<!--  256 KB, a limitation on message size in the Azure Service Bus.  -->
<!-- The snippet checks if the message is smaller than 256000 bytes. If it's larger, request is routed somewhere else. -->

<!-- Copy the following snippet into the inbound section. -->

<policies>
    <inbound>
        <base/>
            <set-variable name="bodySize" value="@(context.Request.Headers["Content-Length"][0])"/>
            <choose>
                <when condition="@(int.Parse(context.Variables.GetValueOrDefault<string>("bodySize"))<256000)">
                    <!-- let it pass through by doing nothing -->
                </when>
                <otherwise>
                    <rewrite-uri template="{{alternate-path-and-query}}"/>
                    <set-backend-service base-url="{{alternate-host}}"/>
                </otherwise>
            </choose>

            <!-- In rare cases where Content-Length header is not present we'll have to read the body to get its length. -->
            <!--
            <choose>
                <when condition="@(context.Request.Body.As<string>(preserveContent: true).Length<256000)">

                </when>
                <otherwise>
                    <rewrite-uri template=""/>
                    <set-backend-service base-url=""/>
                </otherwise>
            </choose>
            -->
    </inbound>
    <backend>
        <base/>
    </backend>
    <outbound>
        <base/>
    </outbound>
    <on-error>
        <base/>
    </on-error>
</policies>
```

## <a name="next-steps"></a>后续步骤

了解有关 APIM 策略的详细信息：

+ [转换策略](../api-management-transformation-policies.md)
+ [策略示例](../policy-reference.md)

