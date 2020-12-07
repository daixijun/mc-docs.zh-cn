---
title: Azure API 管理策略示例 - 实现 X-CSRF 模式
description: Azure API 管理策略示例 - 演示如何实现许多 API 使用的 X-CSRF 模式。 此示例特定于 SAP 网关。
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
ms.openlocfilehash: 7ef922e924f69f1ae5b3ece5a27990764d19999d
ms.sourcegitcommit: f1d0f81918b8c6fca25a125c17ddb80c3a7eda7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2020
ms.locfileid: "96306477"
---
# <a name="implement-x-csrf-pattern"></a>实现 X-CSRF 模式

本文介绍 Azure API 管理策略示例，该示例演示如何实现许多 API 使用的 X-CSRF 模式。 此示例特定于 SAP 网关。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-reference.md)。

## <a name="policy"></a>策略

将代码粘贴到“入站”块中  。

```xml
<!-- The policy defined in this file shows how to implement X-CSRF pattern used by many APIs. The example is specific to SAP Gateway.  -->

<!--    Detailed description of the scenario and solution can be found on: -->
<!--      http://blog.ibiz-solutions.se/uncategorized/exposing-sap-gateway-services-with-api-management/. -->

<!-- Copy the following snippet into the inbound section. -->

<policies>
  <inbound>
    <base/>
    <!-- Set the URL to the service. -->
    <rewrite-uri template="sap/opu/odata/sap/ZCAV_AZURE_CS_ORDER_SRV/ItHeaderSet('{oid}')" />

    <!-- Creating a subrequest "fetchtokenresponse" and set it as GET request to get the token and cookie.-->
    <send-request mode="new" response-variable-name="fetchtokenresponse" timeout="10" ignore-error="false">
      <set-url>@(context.Request.Url.ToString())</set-url>
      <set-method>GET</set-method>
      <set-header name="X-CSRF-Token" exists-action="override">
        <value>Fetch</value>
      </set-header>
      <set-header name="Authorization" exists-action="override">
        <value>{{http-basic-auth-header-value}}</value>
      </set-header>
      <set-body>
      </set-body>
    </send-request>

    <!-- Extract the token from the "fetchtokenresponse" and set as header in the POST request. -->
    <set-header name="X-CSRF-Token" exists-action="skip">
      <value>@(((IResponse)context.Variables["fetchtokenresponse"]).Headers.GetValueOrDefault("x-csrf-token"))</value>
    </set-header>

    <!-- Extract the Cookie from the "fetchtokenresponse" and set as header in the POST request. -->
    <set-header name="Cookie" exists-action="skip">
      <value>
        @{
        string rawcookie = ((IResponse)context.Variables["fetchtokenresponse"]).Headers.GetValueOrDefault("Set-Cookie");
        string[] cookies = rawcookie.Split(';');
        string xsrftoken = cookies.FirstOrDefault( ss => ss.Contains("sap-XSRF"));
        return xsrftoken.Split(',')[1];}
      </value>
    </set-header>
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

